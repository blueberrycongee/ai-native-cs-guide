# RAG

RAG，全称 Retrieval-Augmented Generation，是先检索外部资料，再让模型基于资料生成答案的方法。

它解决的是“模型上下文里没有足够信息”的问题，不是所有幻觉问题的通用解药。

相关概念：

- [[Embedding]]
- [[Context Engineering]]
- [[Context Window]]
- [[LLM]]
- [[Evals]]

## 为什么值得学

很多 AI 项目都需要回答私有文档、代码库、知识库或业务数据里的问题。直接把所有资料放进 prompt 不现实，RAG 就成了常见方案。

但 RAG 项目最容易做成 demo：

- 文档随便切块
- embedding 后扔进向量数据库
- 检索几段内容给模型
- 看起来能回答，但没有引用、没有评测、也不知道错在哪里

真正可用的 RAG 要关心资料质量、切块、召回、重排、引用、拒答和评测。

## 学到什么程度

入门阶段先掌握：

- 文档如何切块
- [[Embedding]] 如何用于相似度检索
- 检索结果如何进入上下文
- 为什么需要引用来源
- 如何用 eval 检查答案是否基于证据

暂时不必比较所有向量数据库。先把小规模 RAG 做到可验证，比换数据库更重要。

## 项目判断标准

一个 RAG 项目至少应该能回答：

- 答案引用了哪些原文
- 找不到证据时会不会拒答
- 错误答案是检索错了，还是生成错了
- 文档更新后索引如何更新
- 用户能否追溯到原始资料

## 资料

- [Retrieval-Augmented Generation paper](https://arxiv.org/abs/2005.11401)：理解原始问题设置。
- [LlamaIndex docs](https://docs.llamaindex.ai/)：看应用层 RAG 组件。
- [LangChain RAG docs](https://python.langchain.com/docs/tutorials/rag/)：看基础链路示例。
