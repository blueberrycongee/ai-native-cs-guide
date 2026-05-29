# Prompting

Prompting 是把任务、约束、上下文和输出格式表达给模型的方式。

在 Agent 系统里，prompt 不等于“写一句提示词”。它会影响模型如何理解目标、选择工具、遵守边界，以及返回能被系统继续处理的结果。

先理解这几件事：

- 任务描述要具体
- 输出格式要可解析
- 工具使用规则要清楚
- 不要把所有知识都硬塞进 prompt
- prompt 需要和评测一起迭代

相关概念：

- [[LLM]]
- [[Context Engineering]]
- [[ReAct]]
- [[Evals]]
