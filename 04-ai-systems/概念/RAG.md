# RAG

RAG，全称 Retrieval-Augmented Generation，是先检索外部资料，再让模型基于资料生成答案的方法。它解决的是“模型上下文里没有足够信息”的问题，不是所有幻觉的通用解药。

一个 RAG 系统真正难的地方不在“把向量库接上模型”，而在证据是否找对、上下文是否干净、答案是否可追溯。

## 工作机制

典型链路是：

```text
documents -> parse -> chunk -> embed -> index
question -> retrieve -> rerank/filter -> build context -> generate answer
```

[[Embedding]] 负责把查询和文档片段映射到向量空间。向量检索找相似片段。重排和过滤再把结果缩小到模型能处理的证据集合。最后由 [[LLM]] 生成答案。

每一步都可能出错。检索错了，模型再强也只能基于错误资料回答。检索对了但上下文拼接差，也会引用混乱。

## 工程形态

一个可用的 RAG 项目至少要有这些组件：

- ingestion：解析 PDF、Markdown、HTML、数据库记录等来源。
- chunking：按语义和 token 预算切块。
- index：保存向量、原文、来源、版本和权限 metadata。
- retriever：支持向量、关键词或 hybrid search。
- reranker：把候选片段重新排序。
- generator：基于证据回答，带引用。
- evaluator：记录检索命中、引用正确性和拒答质量。

向量数据库不是事实来源。原文、权限和版本应该能从源数据重建。否则索引坏了或 embedding 模型更换时，很难恢复。

## 最小项目

先做一个固定文档集问答系统，不要一开始支持所有文件格式。

MVP：

- 选择 20 到 50 篇 Markdown 或网页文档。
- 切块并保存 `source_id`、标题、段落位置和更新时间。
- 使用一个 embedding 模型建立索引。
- 查询时取 top-k，再用简单规则或 reranker 过滤。
- 回答必须带引用；没有证据时拒答。
- 准备 30 个问题，记录 expected source 和人工判断结果。

完成后再加 PDF、多租户、增量更新和 hybrid search。

## 边界和失败模式

RAG demo 最常见的假象是：问几个刚好在文档里的问题，看起来答对了。真正上线后会遇到：

- 用户问法和文档措辞不同，向量召回失败。
- 数字、版本号、代码符号和人名需要关键词检索补充。
- chunk 太小丢上下文，chunk 太大引入噪声。
- 文档更新后旧向量仍被召回。
- 多用户资料没有权限过滤。
- 答案看似有引用，但引用并不支持结论。

因此 RAG 的验收要拆开看：检索有没有找对证据，生成有没有忠实使用证据，系统有没有在证据不足时拒答。

## 参考项目和资料

- [Retrieval-Augmented Generation paper](https://arxiv.org/abs/2005.11401)：原始问题设置。
- [LlamaIndex docs](https://docs.llamaindex.ai/)：看 ingestion、index、query engine 和 evaluation。
- [Haystack docs](https://docs.haystack.deepset.ai/docs/intro)：看 pipeline 和 document store 思路。
- [Qdrant docs](https://qdrant.tech/documentation/)：看向量检索、payload filter 和 hybrid search。
- [Milvus docs](https://milvus.io/docs)：看大规模向量库部署和索引概念。
