# Workflow

Workflow 是把 Agent 放进可控流程里，而不是让模型完全自由发挥。

一个真实 Agent 项目通常需要：

- 明确开始和结束条件
- 把任务拆成可观察的步骤
- 控制哪些步骤能自动执行
- 在高风险动作前加入人工确认
- 记录状态、日志和失败原因

Workflow 主要解决可控性和可维护性问题。

相关概念：

- [[Agent Loop]]
- [[Human in the Loop]]
- [[Single Agent vs Multi-Agent]]
- [[Evals]]
