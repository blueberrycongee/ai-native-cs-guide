# Skills

Skills 是 Agent 系统里的一个常见设计。

可以先这样理解 skill：

> 一个可复用的任务说明、能力包或工作流，让 Agent 在特定任务上少走弯路。

Skill 可以不是代码。很多 skill 是 Markdown 文档，里面写明这个能力什么时候使用、怎么使用、有哪些约束、需要哪些文件或工具。

它解决的是这些问题：

- 不要每次都把所有知识塞进 prompt
- 把重复工作沉淀成可复用能力
- 让 Agent 根据任务按需加载相关说明
- 让团队或个人把工作流标准化

但 skills 也有风险：

- 质量差的 skill 会误导 Agent
- 过多 skill 会污染上下文
- 第三方 skill 可能带来安全和供应链风险
- skill 不等于能力，仍然需要测试和验证

可以重点看 OpenClaw 和 Hermes Agent 的 skills 设计。

## 资源

- [OpenClaw Skills](https://docs.openclaw.ai/tools/skills)
- [Hermes Agent Skills docs](https://hermes-agent.nousresearch.com/docs)

相关概念：

- [[Tool Use]]
- [[Prompt Cache]]
- [[Security]]
