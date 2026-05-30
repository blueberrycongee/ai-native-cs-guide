# RAG 开源项目

这页不做工具大全。RAG 项目很多，先看能帮助理解系统边界的项目和文档。

## LlamaIndex

- 文档：[LlamaIndex RAG](https://developers.llamaindex.ai/python/framework/understanding/rag/)
- 类型：数据接入、索引、查询、Agent/RAG 应用框架

LlamaIndex 适合看 RAG 的阶段划分。它把 RAG 拆成 loading、indexing、storing、querying、evaluation，并区分 Document、Node、Retriever、Node Postprocessor、Response Synthesizer。

适合先看：

- ingestion 和 connector。
- node / chunk 的 metadata。
- retriever 和 postprocessor。
- query engine 和 response synthesizer。
- evaluation。

## Haystack

- 文档：[Haystack docs](https://docs.haystack.deepset.ai/docs/intro)
- 类型：组件化 pipeline 框架

Haystack 适合看 RAG pipeline 如何由组件连接起来。它的 retriever、ranker、document store、pipeline、router 等概念比较适合工程化拆分。

适合先看：

- pipeline 如何组织 indexing 和 querying。
- retriever 的 sparse、dense、hybrid 类型。
- ranker 如何接在初召回之后。
- document store 和 metadata filtering。

## Qdrant

- 文档：[Qdrant docs](https://qdrant.tech/documentation/)
- 类型：向量数据库 / 检索引擎

Qdrant 适合看向量库不只是存向量。payload、filter、payload index、hybrid search、score threshold 都是生产 RAG 会碰到的问题。

适合先看：

- payload 和 payload index。
- filtering。
- hybrid search。
- dense/sparse 向量组合。

## Milvus

- 文档：[Milvus docs](https://milvus.io/docs)
- 类型：向量数据库

Milvus 适合看大规模向量检索、索引类型和部署形态。初学者不必一开始深挖所有索引参数，但要知道向量检索有性能、召回、存储和维护成本。

## Ragas

- 文档：[Ragas metrics](https://docs.ragas.io/en/latest/concepts/metrics/available_metrics/)
- 类型：RAG/LLM 应用评测工具

Ragas 适合看 RAG 评测如何拆成 context precision、context recall、faithfulness、response relevancy 等指标。不要把它当万能判官；它更适合帮助你建立“分层评测”的习惯。

## 选择建议

入门时可以用 LlamaIndex 或 Haystack 快速做出 pipeline。真正理解 RAG 时，要把框架里的模块翻译成自己的系统边界：

- 原文在哪里。
- chunk 怎么生成。
- metadata 怎么过滤。
- retrieval 和 rerank 怎么评测。
- 引用如何证明答案。
- 索引怎么更新和重建。
