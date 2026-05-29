# Single Agent vs Multi-Agent

单 Agent 和 Multi-Agent 不是高低级关系。

单 Agent 常用于：

- 任务目标清楚
- 工具链清楚
- 状态可以集中管理
- 需要减少系统复杂度

Multi-Agent 常用于：

- 任务天然有多个角色
- 需要并行探索
- 需要互相审查
- 需要把不同能力隔离到不同 Agent

但 Multi-Agent 很容易被滥用。很多所谓 multi-agent 项目，本质只是多个 prompt 串联，增加了不稳定性，结果没变好。

判断是否需要 Multi-Agent，可以先问：

- 单 Agent 是否已经做不到？
- 多个 Agent 的角色是否真的不同？
- Agent 之间的通信是否可控？
- 最终结果如何评测？
- 出错时能不能定位是哪一个 Agent 的问题？

## 资源

- [AutoGen paper](https://arxiv.org/abs/2308.08155)
- [AutoGen GitHub](https://github.com/microsoft/autogen)
- [CrewAI GitHub](https://github.com/crewAIInc/crewAI)

相关概念：

- [[Agent Loop]]
- [[Workflow]]
- [[Evals]]
