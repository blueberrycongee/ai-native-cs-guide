# Reranking

Reranking 是在初步召回之后，用更精确但更贵的方法重新排序候选证据。

初召回通常追求 recall：宁可多拿一点候选，也不要漏掉关键证据。rerank 追求 precision：从候选里挑出最应该放进上下文的片段。

## 两阶段检索

典型流程是：

```text
retrieve top 50 or 100 candidates
  -> rerank with query + candidate
  -> select top 5 to 20
  -> pack context
```

初召回可以用向量、BM25 或 [[Hybrid Search]]。reranker 可以是 cross-encoder、专门的 rerank API、LLM ranker，或轻量规则。

## 为什么需要 rerank

向量搜索的 top-k 不一定是最适合回答的证据。它可能召回语义相似但不支持结论的片段。reranker 通常能更细地比较 query 和 candidate，判断哪个片段更相关。

Reranking 也能减少生成成本：不是把 50 个 chunk 全塞给模型，而是用 reranker 先缩到更少、更准的证据。

## 代价

Reranking 会增加延迟和成本。候选越多，rerank 越贵。工程上要用评测决定：

- 初召回取多少候选。
- rerank 后保留多少。
- 是否只对高风险问题 rerank。
- 是否用缓存保存常见 query 的结果。

不要为了“高级”而加 reranker。如果检索集很小、chunk 很干净、top-k 已经稳定命中，rerank 可能收益不大。

## 相关概念

- [[RAG]]
- [[Hybrid Search]]
- [[RAG 评测]]
- [[Context Window]]
