# RL 方向

RL，也就是强化学习，研究智能体如何在环境里通过反馈学习策略。它的核心不是背算法名，而是把 [[Environment]]、[[Policy]]、动作、[[Reward]] 和评测定义清楚。

在 AI Native 语境里，RL 和 [[Post-Train]]、[[Agent]]、代码环境、工具环境会相遇。模型在环境里试错、用 reward 优化输出、用 verifier 训练推理能力，都可以放到 RL 框架里理解。

如果目标是准备大模型 RL Infra 面试，不要只停留在传统 Gym 环境。需要进一步读 [[LLM RL Infra]]，把 GRPO、rollout buffer、verl、DAPO/GSPO、熵崩溃和 eval 串成一条系统链路。

## 方向边界

RL 适合这些问题：

- 有可交互环境。
- 行动会影响后续状态。
- 能定义反馈或奖励。
- 需要通过试错改进策略。

不适合一开始就用 RL 的问题：

- 没有可靠 reward。
- 环境不可复现。
- 任务可以用监督学习或规则直接解决。
- 评测都不清楚，却想通过训练变好。

## 推荐进入顺序

1. 读 [[Environment]]、[[Policy]]、[[Reward]]。
2. 用 Gymnasium 跑一个小环境。
3. 读 CleanRL 的单文件 PPO 或 DQN。
4. 用 Stable-Baselines3 做 baseline。
5. 再读 [[PPO]]、[[Offline RL]] 和 [[Post-Train]] 中的 RLHF/GRPO。

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
