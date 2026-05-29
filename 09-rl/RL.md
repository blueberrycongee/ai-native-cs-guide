# RL

这条路径关注强化学习在 AI 系统里的角色。它和 [[Post-Train]]、[[Agent]]、环境交互、任务评测都有关系。

先读 [[RL]] 方向概念。

## 学习顺序

建议先理解：

- agent、environment、action、reward
- 奖励设计为什么困难
- 离线 benchmark 和真实环境的差异
- RLHF、DPO 等方法和大模型训练的关系

不要一开始刷算法名词。没有环境和反馈设计，算法很难落地。

## 入门项目

可以从一个小环境开始：

- 设计一个明确任务
- 定义动作空间和奖励
- 训练或模拟一个简单策略
- 观察模型是否学会钻奖励漏洞

这个练习的重点不是效果多好，而是理解 reward hacking 为什么真实存在。

## 资料

- [Reinforcement Learning: An Introduction](http://incompleteideas.net/book/the-book-2nd.html)
- [OpenAI Spinning Up](https://spinningup.openai.com/)
- [InstructGPT paper](https://arxiv.org/abs/2203.02155)
