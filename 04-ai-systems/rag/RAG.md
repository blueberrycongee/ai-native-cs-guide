# RAG

RAG，全称 Retrieval-Augmented Generation。不要把它理解成“向量库接一个模型”，更准确的说法是：

> RAG 是一个证据选择与上下文构造系统。它先从外部资料中找证据，再把证据组织成模型可用的上下文，最后要求模型基于这些证据回答。

它解决的是模型当前上下文里没有足够信息的问题。它不能自动解决所有幻觉，也不能替代权限、数据治理、评测和产品规则。

## 基本链路

一个 RAG 系统通常有两条链路。

离线或异步索引链路：

```text
source documents
  -> parse
  -> clean
  -> chunk
  -> enrich metadata
  -> embed / lexical index
  -> store index
```

在线查询链路：

```text
user question
  -> query rewrite / decomposition
  -> retrieve candidates
  -> filter by metadata and permissions
  -> rerank
  -> pack context with citations
  -> generate answer
  -> evaluate / log
```

这两条链路要分开理解。索引链路决定“系统知道什么、怎么找”；查询链路决定“本次问题用哪些证据、怎么回答”。

## 核心判断

现代 RAG 的核心不是向量检索，而是证据选择。

向量检索适合处理语义相似，但它对数字、版本号、代码符号、人名、产品名、错误码、权限过滤和时间过滤并不天然可靠。生产系统通常会组合 [[Hybrid Search]]、[[Metadata Filtering]]、[[Reranking]] 和 [[Citation]]。

RAG 也不是 [[Agent]]。普通 RAG pipeline 是 workflow：路径基本固定，系统按预设步骤检索、过滤、生成。只有当模型能根据中间 observation 动态决定下一步查什么、是否继续、是否换工具时，它才开始变得 agentic。

## 目录入口

先按这个顺序读：

1. [[RAG 工程架构]]：看一个 RAG 系统拆成哪些组件。
2. [[Chunking]]：理解为什么切块决定召回上限。
3. [[Hybrid Search]]：理解为什么不能只靠向量相似度。
4. [[Reranking]]：理解为什么先粗召回再精排。
5. [[Metadata Filtering]]：理解权限、时间和版本为什么必须进索引。
6. [[Citation]]：理解引用不是把来源拼在答案后面。
7. [[RAG 评测]]：把检索质量和回答质量拆开测。
8. [[RAG 开源项目]]：选择框架、向量库和评测工具时看什么。

## 最小项目

第一个 RAG 项目不要一开始支持所有文件格式。选择一个固定文档集，例如 20 到 50 篇 Markdown 或网页文档，先做闭环：

- 解析文档，保存 `doc_id`、标题、来源 URL、更新时间。
- 切块，保存 `chunk_id`、`doc_id`、段落位置、权限和版本。
- 建立向量索引，同时保留关键词检索或 BM25 能力。
- 查询时取候选片段，按权限和时间过滤，再 rerank。
- 回答必须带引用；证据不足时拒答。
- 准备 30 个问题，记录 expected source、检索命中、引用是否支持结论。

能跑通这个闭环之后，再加 PDF、多租户、增量更新、复杂 query rewrite 和更重的评测工具。

## 参考资料

- [Retrieval-Augmented Generation paper](https://arxiv.org/abs/2005.11401)：原始问题设置。
- [LlamaIndex RAG docs](https://developers.llamaindex.ai/python/framework/understanding/rag/)：看 loading、indexing、storing、querying、evaluation 五个阶段。
- [Haystack pipelines](https://docs.haystack.deepset.ai/docs/pipelines)：看组件化 RAG pipeline 的组织方式。
- [Anthropic Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)：看 chunk 丢上下文后如何用 contextual embeddings、BM25 和 rerank 改进召回。
