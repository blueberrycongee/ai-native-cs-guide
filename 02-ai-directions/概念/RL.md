# RL

RL，也就是强化学习，研究智能体如何在环境里通过反馈学习策略。

在这个仓库里，RL 不是从传统算法清单讲起，而是放在 AI Native 的语境里看：模型如何从奖励、偏好、环境交互和任务结果中改进行为。

相关概念：

- [[Agent]]
- [[Post-Train]]
- [[Evals]]
- [[Tool Use]]

## 为什么值得学

RL 的难点不只是算法。更难的是定义环境、动作、奖励和评测。

在大模型项目里，它常出现在这些地方：

- 用人类偏好改进模型输出
- 让模型在代码、网页或工具环境里试错
- 用任务结果作为反馈优化策略
- 设计 eval，避免模型只学会刷分

所以 RL 和 [[Post-Train]]、[[Agent]] 的边界经常会重叠。

## 入门边界

先理解：

- agent、environment、action、reward 是什么
- 为什么奖励设计会影响行为
- 为什么离线 benchmark 不等于真实能力
- RLHF、DPO 等方法大概想解决什么问题

暂时不必先刷完所有传统 RL 算法。Q-learning、policy gradient、actor-critic 这些重要，但如果没有应用场景，很容易变成只背名词。

更完整的路径入口见 [[RL 路径]]。

## 资料

- [Sutton and Barto: Reinforcement Learning](http://incompleteideas.net/book/the-book-2nd.html)：经典教材，适合系统学习。
- [OpenAI Spinning Up](https://spinningup.openai.com/)：适合理解传统 RL 基础。
- [InstructGPT paper](https://arxiv.org/abs/2203.02155)：看 RLHF 如何进入指令模型训练。
