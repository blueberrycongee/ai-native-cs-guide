# ReAct

ReAct 是一篇经典论文提出的 Agent 模式：把 reasoning 和 acting 交替组织起来。它的价值不是提供固定 prompt，而是说明很多任务需要“想一步、做一步、看结果、再修正”。

## 工作机制

ReAct 的基本结构是：

```text
Reason -> Act -> Observe -> Reason -> ...
```

Reason 是模型判断下一步要做什么。Act 是选择工具或行动。Observe 是系统把工具结果返回给模型。这和 [[Agent Loop]] 是同一类思想。

## 工程形态

适合 ReAct 的任务通常需要外部信息或外部动作：

- 搜索资料后回答。
- 读文件后修改代码。
- 查询数据库后生成报告。
- 调用工具失败后换策略。

不需要外部反馈的任务，不一定要做成 ReAct。普通生成或一次结构化抽取可能更简单。

## 边界和失败模式

常见失败包括：

- 把 reasoning 全部暴露给用户，泄露内部策略或无关内容。
- 工具结果没有结构化，模型难以继续。
- 没有停止条件，ReAct 变成无限循环。
- 迷信论文 prompt 格式，不做权限、日志和 eval。

ReAct 是理解 Agent 的入口，不是生产系统的完整架构。

## 参考资料

- [ReAct paper](https://arxiv.org/abs/2210.03629)：原始论文。
- [LangChain agents concepts](https://python.langchain.com/docs/concepts/agents/)：看框架中 agent 和 tool 的抽象。
