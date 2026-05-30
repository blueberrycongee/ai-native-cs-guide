# Hybrid Search

Hybrid Search 是把多种检索方式组合起来。最常见的是 dense vector search 加 keyword / BM25，或者 dense embedding 加 sparse embedding。

它解决的问题很直接：向量检索擅长语义相似，但不擅长所有精确匹配。

## 为什么不能只靠向量

向量检索容易漏掉：

- 错误码，例如 `TS-999`。
- API 名、函数名、配置项。
- 版本号、日期、金额、订单号。
- 人名、产品名、专有名词。
- 用户原话里非常关键的短语。

这些信息往往更适合关键词检索。Anthropic 的 Contextual Retrieval 文章也强调，BM25 能补上 embedding 对精确文本匹配不敏感的问题。

## 常见组合

```text
query
  -> dense retrieval
  -> keyword / BM25 retrieval
  -> merge and deduplicate
  -> rank fusion
  -> rerank
```

组合方式可以很简单：

- union：把多个检索结果合并去重。
- weighted score：给不同检索器分数加权。
- reciprocal rank fusion：按排名融合，不依赖不同检索器分数尺度一致。
- router：根据 query 类型选择一个或多个 retriever。

## 工程注意点

Hybrid Search 不是无脑加复杂度。它会增加索引维护、查询延迟、调参和评测成本。适合在这些场景使用：

- 文档里有大量代码、配置、错误码或产品名。
- 用户问题常包含精确短语。
- 只用向量检索时 recall@k 明显不足。
- 需要在语义相似和精确匹配之间平衡。

如果语料很小，直接全文放入 prompt 或简单关键词检索可能更稳。

## 相关概念

- [[RAG]]
- [[Reranking]]
- [[Metadata Filtering]]
- [[RAG 评测]]
