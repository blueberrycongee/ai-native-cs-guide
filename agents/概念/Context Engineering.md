# Context Engineering

Context Engineering 关心模型在一次任务中应该看到什么、不应该看到什么，以及这些信息按什么顺序进入上下文。

在 Agent 系统里，上下文通常包括：

- 系统提示词
- 用户目标
- 当前状态
- 工具说明
- skills 内容
- 检索结果
- 历史步骤
- 错误信息和评测反馈

目标不是“塞更多内容”。要在有限上下文里放进更相关、更稳定、可执行的信息。

相关概念：

- [[Prompting]]
- [[RAG]]
- [[Skills]]
- [[Memory]]
- [[Prompt Cache]]
