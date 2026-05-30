# RL 方向

RL，也就是强化学习，研究智能体如何在环境里通过反馈学习策略。它的核心不是背算法名，而是把 [[Environment]]、[[Policy]]、动作、[[Reward]] 和评测定义清楚。

在 AI Native 语境里，RL 和 [[Post-Train]]、[[Agent]]、代码环境、工具环境会相遇。模型在环境里试错、用 reward 优化输出、用 verifier 训练推理能力，都可以放到 RL 框架里理解。

大模型 RL Infra 会把这些问题带到更复杂的系统里：GRPO、rollout buffer、verl、DAPO/GSPO、熵崩溃和 eval 不再只是算法概念，而是训练链路里的工程组件。

## 方向边界

RL 常出现的前提：

- 有可交互环境。
- 行动会影响后续状态。
- 能定义反馈或奖励。
- 需要通过试错改进策略。

另一些问题不一定需要 RL：

- reward 不可靠。
- 环境不可复现。
- 监督学习或规则已经能直接解决。
- 评测定义还不清楚。

## 目录入口

- [[RL 路径]]
- [[RL 信息来源]]
- [[RL 开源项目]]
- [[RL 框架和工具]]
- [[Environment]]
- [[Policy]]
- [[Reward]]
- [[PPO]]
- [[Offline RL]]
- [[LLM RL Infra]]
