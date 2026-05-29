# Agent Loop

Agent Loop 是 Agent 的工作循环。它让模型不是一次性回答，而是在目标、上下文、工具结果和反馈之间反复推进。

## 工作机制

一个最小循环是：

```text
observe -> decide -> act -> observe result -> decide next step
```

在代码里，它通常表现为有限轮数的 while loop。每一轮都要记录输入、模型输出、工具调用、工具结果和停止原因。

## 工程形态

以 coding agent 为例：

1. 读任务。
2. 搜索相关文件。
3. 修改代码。
4. 运行测试。
5. 根据失败信息继续修改。
6. 测试通过或达到边界后停止。

这个循环的价值不在“模型会思考”，而在它能把外部反馈纳入下一步行动。

## 最小例子

```python
for step in range(max_steps):
    decision = model.decide(state)
    if decision.type == "final":
        return decision.answer
    result = run_tool(decision.tool, decision.args)
    state.add_observation(result)
```

真实系统还要处理取消、超时、权限、工具错误和日志。

## 边界和失败模式

常见失败包括：

- 没有最大轮数，循环烧成本。
- 工具失败没有回到上下文，模型反复犯同一个错。
- 停止条件模糊，任务完成了还继续行动。
- 日志不完整，无法复盘为什么调用某个工具。
- 最终答案不引用工具结果。

一个可用 loop 必须可停止、可追踪、可恢复部分失败。

## 参考资料

- [ReAct paper](https://arxiv.org/abs/2210.03629)：看 reasoning 和 acting 如何交替。
- [LangGraph docs](https://docs.langchain.com/oss/python/langgraph)：看有状态、可恢复的循环和图执行。
