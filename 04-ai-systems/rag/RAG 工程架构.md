# RAG 工程架构

RAG 的工程架构可以拆成索引侧和查询侧。这里说的是经典 RAG：先把文档切块并向量化，查询时召回候选片段，排序或重排后交给模型生成答案。

很多 RAG demo 失败，是因为只写了查询侧：把文档切一切、向量搜一搜、塞进 prompt。真实系统更重要的是数据来源、权限、版本、更新和评测。

## 索引侧

索引侧负责把原始资料变成可检索证据。

```text
connector -> parser -> cleaner -> chunker -> metadata builder -> embedder -> index writer
```

每一层都有明确责任：

- connector：从文件、网页、数据库、API、对象存储拿到源数据。
- parser：把 PDF、HTML、Markdown、表格、代码等格式解析成结构化文本。
- cleaner：去掉导航、页眉页脚、重复水印、乱码和无意义空白。
- chunker：决定 retrieval unit，也就是一次召回的最小证据单位。
- metadata builder：写入 `doc_id`、`chunk_id`、来源、时间、版本、权限、租户、段落位置。
- embedder：生成向量，必要时同时生成 sparse embedding 或关键词索引。
- index writer：写入向量库、搜索引擎或混合索引。

原文才是事实来源。向量库是可重建索引，不应该成为唯一数据源。换 embedding 模型、改 chunk 策略、修 parser bug 时，系统应该能从原文重建索引。

## 查询侧

查询侧负责把用户问题变成可验证回答。

```text
question -> query embedding -> retrieval -> filtering -> ranking/reranking -> context packing -> generation -> logging
```

查询侧最容易出问题的是把所有逻辑都塞进 prompt。更稳的边界是：

- query embedding：把用户问题编码成检索向量，必要时提取少量关键词。
- retrieval：用向量检索取候选，必要时结合关键词或混合检索。
- filtering：按租户、权限、文档类型、时间、版本过滤。
- reranking：让更精确但更贵的模型重新排序候选证据。
- context packing：按引用、来源、顺序和 token 预算组织上下文。
- generation：只基于证据回答，证据不足时拒答。
- logging：记录 query、候选、过滤原因、rerank 分数、引用和最终答案。

## 服务边界

一个可维护的 RAG 服务通常至少需要这些边界：

- ingestion job：可重跑、可增量更新、可回滚。
- source store：保存原文和版本。
- index store：保存向量、关键词索引和 metadata。
- retrieval API：只返回当前用户有权限看到的候选证据。
- generation API：把证据、问题和格式约束交给模型。
- eval dataset：固定样例，用来比较 chunk、embedding、top-k、rerank 和 prompt 改动。

如果这些东西都在一个脚本里，适合学习，不适合长期维护。

## 常见架构错误

- 解析质量没检查，表格、代码块和标题层级已经坏了，还继续 embedding。
- chunk 没有 `doc_id`、段落位置和更新时间，导致引用和更新不可控。
- metadata 只用于展示，不参与过滤，权限隔离靠 prompt。
- 向量库里只有 chunk 文本，没有可回源的原文和版本。
- 检索、重排、生成没有 trace，失败时只能猜是模型问题还是检索问题。
- 文档更新只追加新向量，不删除或废弃旧 chunk。

## 相关概念

- [[Chunking]]
- [[Hybrid Search]]
- [[Metadata Filtering]]
- [[Reranking]]
- [[Citation]]
- [[RAG 评测]]
