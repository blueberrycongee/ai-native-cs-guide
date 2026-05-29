# Agent Loop

先从 Agent loop 看起。

普通 chatbot 通常只做一次“输入 -> 输出”。Agent 会根据中间结果继续行动，比如读文件、检索资料、调用 API、运行命令、等待用户确认，然后再决定下一步。

一个简化的 loop 是：

```text
observe -> think / plan -> act -> get feedback -> update state -> continue or stop
```

要理解 Agent，先看 loop，不要一上来就学框架。

## 关键问题

- Agent 如何知道下一步该做什么
- 什么时候继续，什么时候停止
- 工具失败后如何恢复
- 中间状态存在哪里
- 哪些动作需要用户确认

相关概念：

- [[ReAct]]
- [[Tool Use]]
- [[State]]
- [[Human in the Loop]]
- [[Evals]]
