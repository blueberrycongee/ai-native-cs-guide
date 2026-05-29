# ReAct

ReAct 是一篇经典论文提出的 Agent 推理方式：把 reasoning 和 acting 交替组织起来。

它的价值不是提供一个永远正确的 prompt 模板，而是说明很多任务需要模型先思考、再行动、再根据观察结果修正。

相关概念：

- [[Agent]]
- [[Agent Loop]]
- [[Tool Use]]
- [[Prompting]]
- [[Evals]]

## 为什么值得学

没有外部行动时，模型只能基于已有上下文回答。任务一旦需要搜索、计算、读文件或调用 API，就会进入“想一步、做一步、再看结果”的模式。

ReAct 给初学者一个清楚框架：

- Reason：模型判断当前该做什么
- Act：模型选择工具或行动
- Observe：系统把工具结果返回给模型

这就是 [[Agent Loop]] 的早期经典表达。

## 学到什么程度

先理解模式，不要迷信格式。

你需要知道：

- ReAct 适合需要多步外部信息的任务
- 工具结果必须可靠地回到上下文
- reasoning 文本不一定要暴露给用户
- 复杂任务需要停止条件和 eval

不需要一开始复现论文实验。做一个能搜索资料再回答的小 Agent，更能建立直觉。

## 资料

- [ReAct paper](https://arxiv.org/abs/2210.03629)：原始论文。
- [LangChain ReAct agents](https://python.langchain.com/docs/concepts/agents/)：看框架里如何抽象这种模式。
