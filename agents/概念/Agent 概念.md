# Agent 概念

这是 Agent 方向的概念入口。

这里不把每个概念讲细，只先画地图：想进入 Agent 方向，至少要知道哪些概念，以及它们之间是什么关系。

## 最小概念地图

可以先按这条线理解 Agent：

```text
LLM
  -> Prompt / Context
  -> Tool Use
  -> Agent Loop
  -> Memory
  -> RAG
  -> Skills
  -> Workflow / Human in the Loop
  -> Evals / Security / Cost
```

再展开一点：

- [[LLM]] 提供推理和生成能力
- [[Prompting]] 和 [[Context Engineering]] 决定模型在当前任务里看到什么
- [[Tool Use]] 让模型能调用外部能力
- [[Agent Loop]] 把观察、推理、行动、反馈组织成循环
- [[Memory]] 让 Agent 能跨步骤、跨会话保留必要信息
- [[RAG]] 让 Agent 能从外部知识库拿信息
- [[Skills]] 把可复用能力封装成可被 Agent 调用的知识或工具包
- [[Workflow]] 和 [[Human in the Loop]] 让 Agent 进入真实业务流程
- [[Evals]]、[[Security]]、[[Prompt Cache]] 决定系统能不能稳定、可控、低成本地运行

## 必读概念

- [[Agent Loop]]
- [[ReAct]]
- [[Tool Use]]
- [[RAG]]
- [[Skills]]
- [[Single Agent vs Multi-Agent]]
- [[Prompt Cache]]

## 学到什么程度算入门

能回答这些问题，就算入门：

- Agent 和 chatbot 的区别是什么
- ReAct 为什么要学
- tool use 解决什么问题
- RAG 在 Agent 里负责什么
- skill 和 prompt、tool、MCP 有什么区别
- 单 Agent 和 Multi-Agent 应该怎么选
- prompt cache 为什么会影响成本和延迟
- 一个 Agent 项目为什么需要 eval 和安全边界

一开始不需要手写完整框架。先看懂真实项目，再做一个小项目。
