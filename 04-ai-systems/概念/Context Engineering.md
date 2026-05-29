# Context Engineering

Context Engineering 是为模型组织上下文的工程方法。它关心哪些信息进入 [[Context Window]]、以什么顺序进入、如何压缩、如何更新，以及如何避免无关信息污染任务。

它比 [[Prompting]] 更宽。Prompting 偏任务说明，context engineering 还包括检索、记忆、工具结果、历史消息和系统状态。

相关概念：

- [[Context Window]]
- [[RAG]]
- [[Prompt Cache]]
- [[Agent]]
- [[Memory]]

## 为什么值得学

LLM 的能力很大程度取决于它看到什么。上下文放错，模型可能不是不会，而是没拿到该拿的信息。

常见问题：

- 把太多历史消息塞进 prompt，关键信息被淹没
- [[RAG]] 检索结果相关性不够，模型只能硬答
- 工具返回太长，Agent 后续步骤变慢
- 系统状态没有结构化，模型很难稳定接着做

## 学到什么程度

入门阶段先掌握：

- 区分任务说明、用户输入、检索资料、工具结果和历史状态
- 控制每类上下文的 token 预算
- 对长任务做摘要和状态压缩
- 保留可追溯证据，不让模型只凭记忆回答
- 用 eval 检查上下文策略是否真的改善结果

暂时不用设计复杂 memory 系统。很多项目先做好检索、摘要和日志，就能解决大部分问题。

## 资料

- [OpenAI Prompt Caching](https://platform.openai.com/docs/guides/prompt-caching)：理解长上下文的成本优化。
- [Anthropic Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval)：看检索前给 chunk 补上下文的一种做法。
