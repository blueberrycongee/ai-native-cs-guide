# RAG 开源项目

RAG 项目可以按应用框架、pipeline 框架、向量数据库和评测工具几类来看。

## LlamaIndex

- 文档：[LlamaIndex RAG](https://developers.llamaindex.ai/python/framework/understanding/rag/)

LlamaIndex 是数据接入、索引、查询和 RAG 应用框架。它把 loading、indexing、storing、querying、evaluation 这几个阶段，以及 Document、Node、Retriever、Node Postprocessor、Response Synthesizer 等抽象放在一起。

## Haystack

- 文档：[Haystack docs](https://docs.haystack.deepset.ai/docs/intro)

Haystack 是组件化 pipeline 框架，用 retriever、ranker、document store、pipeline、router 等组件组织 indexing 和 querying。sparse / dense / hybrid retriever、ranker、document store、metadata filtering 都是它的核心组成。

## Qdrant

- 文档：[Qdrant docs](https://qdrant.tech/documentation/)

Qdrant 是向量数据库和检索引擎，围绕向量存储、payload、filter、payload index、hybrid search、score threshold 展开，也覆盖 dense/sparse 向量组合、metadata filtering 和 hybrid search。

## Milvus

- 文档：[Milvus docs](https://milvus.io/docs)

Milvus 是向量数据库，主要呈现大规模向量检索、索引类型、部署形态和存储维护等问题。

## Ragas

- 文档：[Ragas metrics](https://docs.ragas.io/en/latest/concepts/metrics/available_metrics/)

Ragas 是 RAG/LLM 应用评测工具，把 RAG 评测拆成 context precision、context recall、faithfulness、response relevancy 等指标，和 [[RAG 评测]]、分层评测、答案忠实度、检索质量直接相关。
