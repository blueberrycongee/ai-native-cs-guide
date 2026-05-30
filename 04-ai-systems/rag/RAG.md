# RAG

RAG，全称 Retrieval-Augmented Generation。这个模块先按经典 RAG 来讲：把外部资料切块并向量化，查询时召回相关片段，排序或重排后放进模型上下文，再让模型基于这些片段生成答案。

它首先是一种方法，不是一整套 Agent 架构。后续确实发展出了 Agentic RAG、GraphRAG、多步检索、自主查询规划等技术，但这些不作为本文的讨论范围。本文只关心最基础也最常用的这条线：

```text
chunk -> embed -> retrieve -> rank/rerank -> generate
```

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
  -> embed
  -> store index
```

在线查询链路：

```text
user question
  -> embed query
  -> retrieve candidate chunks
  -> filter by metadata and permissions
  -> rank / rerank
  -> pack context with citations
  -> generate answer
  -> evaluate / log
```

这两条链路要分开理解。索引链路决定“系统知道什么、怎么找”；查询链路决定“本次问题用哪些证据、怎么回答”。

## 核心判断

这个模块讨论的 RAG 主线就是向量化后的召回和排序。难点不在“是否用了向量库”，而在向量化、召回、排序和上下文拼接是否真的把正确证据送到了模型面前。

向量检索适合处理语义相似，但它对数字、版本号、代码符号、人名、产品名、错误码、权限过滤和时间过滤并不天然可靠。因此经典 RAG 往往还会补上 [[Hybrid Search]]、[[Metadata Filtering]]、[[Reranking]] 和 [[Citation]]。这些仍然属于“把证据找准、排好、放进上下文”的问题，不等于 Agentic RAG。

RAG 和 [[Agent]] 的边界也要分清。普通 RAG pipeline 路径基本固定，系统按预设步骤检索、过滤、排序、生成。只有当模型能根据中间结果动态决定下一步查什么、是否继续、是否换工具时，它才进入 agentic RAG 的范围；这部分放到 Agent 或进阶专题里讨论。

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

[[Query Rewrite]] 可以作为检索前处理了解，但它不是这篇 RAG 主线的核心。先把向量化、召回、排序、引用和评测做好，再考虑复杂查询改写或多步检索。

## 最小项目

第一个 RAG 项目不要一开始支持所有文件格式。选择一个固定文档集，例如 20 到 50 篇 Markdown 或网页文档，先做闭环：

- 解析文档，保存 `doc_id`、标题、来源 URL、更新时间。
- 切块，保存 `chunk_id`、`doc_id`、段落位置、权限和版本。
- 建立向量索引，同时保留关键词检索或 BM25 能力。
- 查询时取候选片段，按权限和时间过滤，再 rerank。
- 回答必须带引用；证据不足时拒答。
- 准备 30 个问题，记录 expected source、检索命中、引用是否支持结论。

能跑通这个闭环之后，再加 PDF、多租户、增量更新和更重的评测工具。复杂 query rewrite、agentic RAG 和多步检索不要放在第一个版本里。

## 参考资料

- [Retrieval-Augmented Generation paper](https://arxiv.org/abs/2005.11401)：原始问题设置。
- [LlamaIndex RAG docs](https://developers.llamaindex.ai/python/framework/understanding/rag/)：看 loading、indexing、storing、querying、evaluation 五个阶段。
- [Haystack pipelines](https://docs.haystack.deepset.ai/docs/pipelines)：看组件化 RAG pipeline 的组织方式。
- [Anthropic Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval)：看 chunk 丢上下文后如何用 contextual embeddings、BM25 和 rerank 改进召回。
