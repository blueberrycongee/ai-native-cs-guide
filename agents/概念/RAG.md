# RAG

RAG 是 Retrieval-Augmented Generation，检索增强生成。

对 Agent 来说，RAG 的作用很直接：让模型别只靠参数记忆，而是从外部知识源拿当前任务需要的信息。

Agent 里常见的 RAG 场景：

- 读取本地 Markdown 笔记
- 查询公司文档
- 检索代码库说明
- 搜索 issue、PR、commit
- 从网页或论文里找依据

注意：RAG 不是“加一个向量数据库”就结束。真正影响效果的是文档切分、召回质量、rerank、引用来源、更新机制和评测。

## 资源

- [RAG paper](https://arxiv.org/abs/2005.11401)

相关概念：

- [[Context Engineering]]
- [[Memory]]
- [[Evals]]
