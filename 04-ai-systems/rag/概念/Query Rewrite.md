# Query Rewrite

Query Rewrite 是在检索前改写用户问题，让它更适合搜索。它不是为了让问题看起来更漂亮，而是为了提高召回。

用户问题常常不等于检索查询。用户会省略上下文、使用代词、混合多个问题，或者用和文档完全不同的说法提问。

## 常见做法

- rewrite：把口语问题改成更清楚的搜索查询。
- keyword extraction：提取产品名、错误码、函数名、版本号。
- decomposition：把复杂问题拆成多个子查询。
- multi-query：生成多个等价问法，分别检索后合并。
- hypothetical answer / document：先生成一个可能答案或文档，再用它辅助检索。

## 什么时候需要

适合 query rewrite 的情况：

- 用户问题依赖前文，例如“这个怎么配置？”
- 一个问题包含多个条件。
- 需要同时查概念解释和具体版本。
- 文档措辞和用户措辞差异很大。
- 初始检索经常漏掉 expected source。

不适合过度 rewrite 的情况：

- 查询里有精确错误码、订单号、函数名。
- 用户原文就是最佳关键词。
- rewrite 模型容易改掉关键限制。

## 工程边界

Query Rewrite 的输出应该被记录，并进入评测。否则检索失败时，你不知道是原问题难、改写错，还是 retriever 不行。

可以记录：

```text
original_query
rewritten_queries
extracted_keywords
filters
retrieval_results_by_query
```

## 相关概念

- [[RAG]]
- [[Hybrid Search]]
- [[RAG 评测]]
