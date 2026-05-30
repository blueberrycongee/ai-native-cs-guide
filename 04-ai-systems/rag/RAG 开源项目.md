# RAG 开源项目

RAG 项目可以按应用框架、pipeline 框架、向量数据库和评测工具几类来看。

## LlamaIndex

- 文档：[LlamaIndex RAG](https://developers.llamaindex.ai/python/framework/understanding/rag/)
- 类型：数据接入、索引、查询和 RAG 应用框架
- 代表形态：loading、indexing、storing、querying、evaluation 这几个阶段，以及 Document、Node、Retriever、Node Postprocessor、Response Synthesizer 等抽象。
- 相关主题：ingestion、connector、node metadata、retriever、postprocessor、query engine、response synthesizer、evaluation。

## Haystack

- 文档：[Haystack docs](https://docs.haystack.deepset.ai/docs/intro)
- 类型：组件化 pipeline 框架
- 代表形态：用 retriever、ranker、document store、pipeline、router 等组件组织 indexing 和 querying。
- 相关主题：sparse / dense / hybrid retriever、ranker、document store、metadata filtering。

## Qdrant

- 文档：[Qdrant docs](https://qdrant.tech/documentation/)
- 类型：向量数据库 / 检索引擎
- 代表形态：向量存储、payload、filter、payload index、hybrid search、score threshold。
- 相关主题：dense/sparse 向量组合、metadata filtering、hybrid search。

## Milvus

- 文档：[Milvus docs](https://milvus.io/docs)
- 类型：向量数据库
- 代表形态：大规模向量检索、索引类型、部署形态和存储维护。
- 相关主题：索引、召回、性能、存储成本、集群部署。

## Ragas

- 文档：[Ragas metrics](https://docs.ragas.io/en/latest/concepts/metrics/available_metrics/)
- 类型：RAG/LLM 应用评测工具
- 代表形态：把 RAG 评测拆成 context precision、context recall、faithfulness、response relevancy 等指标。
- 相关主题：[[RAG 评测]]、分层评测、答案忠实度、检索质量。
