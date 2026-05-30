# RAG 评测

RAG 评测必须拆开看。一个系统答错，可能是没召回证据、召回了但排序太低、引用不支持结论、模型没忠实使用证据，或者本来就应该拒答。

不要只看最终答案分数。RAG 至少要评测三层：

- retrieval：有没有找对证据。
- grounding：答案是否被证据支持。
- behavior：证据不足、权限不足、资料过期时是否正确拒答。

## 样例结构

一个最小评测集可以这样记录：

```text
id
question
expected_sources
required_facts
forbidden_sources
should_answer
acceptable_answer
notes
```

`expected_sources` 用来测检索；`required_facts` 用来测答案是否覆盖关键事实；`should_answer` 用来测拒答。不要只保存一个“标准答案”，否则很难定位错误发生在哪一层。

## 检索指标

检索指标关注候选证据，不看最终生成。

- hit@k：正确来源是否进入 top-k。
- recall@k：需要的证据有多少被找回。
- precision@k：top-k 里有多少是真相关。
- MRR：第一个正确结果排在多靠前。
- permission leakage：是否召回了当前用户无权访问的资料。
- freshness error：是否召回了旧版本或已删除文档。

调 chunk、embedding、hybrid search、metadata filter 和 rerank 时，这些指标能定位检索问题。

## 回答指标

回答指标关注模型是否忠实使用证据。

- faithfulness：答案有没有超出证据。
- citation support：引用片段是否真的支持对应结论。
- answer relevance：答案是否回答了用户问题。
- abstention quality：证据不足时是否拒答，而不是编。
- format correctness：引用、列表、字段、JSON 等格式是否稳定。

Ragas 文档把 RAG 相关指标拆成 Context Precision、Context Recall、Response Relevancy、Faithfulness 等，这个方向是对的：先分层，再看最终体验。

## 调参顺序

排查 RAG 质量时，建议按这个顺序：

1. 固定模型和 prompt，只评测 retrieval。
2. 调 chunk 大小、overlap、标题上下文、metadata。
3. 比较 vector、BM25、hybrid search。
4. 加 reranker，看 hit@k 和 citation support 是否提升。
5. 最后再改 prompt 和生成模型。

如果检索证据本身错了，换更强生成模型通常只是让错误答案更流畅。

## 参考资料

- [Ragas metrics](https://docs.ragas.io/en/latest/concepts/metrics/available_metrics/)：看 RAG 指标如何拆分。
- [LlamaIndex evaluation docs](https://developers.llamaindex.ai/python/framework/understanding/rag/)：看 RAG flow 里为什么 evaluation 是独立阶段。
- [[RAG 工程架构]]
- [[Citation]]
