# Single Agent vs Multi-Agent

默认先做 Single Agent。Multi-Agent 只有在任务边界、权限边界或评审机制真的需要拆开时才值得引入。

相关概念：

- [[Agent]]
- [[Agent Loop]]
- [[Tool Use]]
- [[Evals]]

## 为什么先从单 Agent 开始

单 Agent 更容易调试：

- 上下文在哪里
- 工具是谁调用的
- 错误从哪一步开始
- 成本花在哪一轮
- eval 失败归因到哪里

这些问题在单 Agent 里都不容易。多 Agent 会把它们放大。

## 什么时候考虑多 Agent

多 Agent 可能有价值的场景：

- 需要明确分离角色，比如执行者和审查者
- 需要隔离权限，比如只读 Agent 和可写 Agent
- 任务天然可以并行，比如多资料源检索
- 需要对抗式检查，比如生成方案后由另一个 Agent 挑错

如果只是想让系统“看起来更智能”，不要拆。

## 常见误判

很多 multi-agent demo 实际上只是多个 prompt 轮流发言。它们不一定比一个设计良好的 Agent 更可靠。

判断是否值得拆分，要看拆分后有没有带来可验证收益：

- 成功率提高了吗
- 错误更容易定位了吗
- 高风险权限更好控制了吗
- 成本和延迟还能接受吗

没有这些收益，多 Agent 只是复杂度。

## 资料

- [AutoGen GitHub](https://github.com/microsoft/autogen)：观察 multi-agent conversation 框架。
- [LangGraph GitHub](https://github.com/langchain-ai/langgraph)：看有状态图编排如何处理多步骤任务。
