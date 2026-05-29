# RL 路径

RL，也就是强化学习，关注智能体如何在环境里通过反馈学习策略。在 AI Native 语境里，它和 [[Post-Train]]、[[Agent]]、代码环境、工具环境和任务评测经常交叉。

## 学习顺序

1. 先理解 agent、[[Environment]]、action、[[Reward]]、[[Policy]]、trajectory。
2. 用 Gymnasium 跑一个小环境，观察状态、动作和奖励。
3. 读 CleanRL 的单文件 PPO 或 DQN，别急着背算法名。
4. 用 Stable-Baselines3 做可靠 baseline。
5. 再把 RL 连接回 [[Post-Train]]：[[RLHF]]、[[GRPO]]、[[Reward Model]] 和 eval。

RL 的难点不只是算法。更难的是定义环境和奖励。

## 必做项目

做一个小环境实验：

- 选 Gymnasium 的 CartPole、LunarLander 或一个自定义网格环境。
- 明确定义观察、动作、奖励和终止条件。
- 用 Stable-Baselines3 或 CleanRL 训练一个 baseline。
- 改奖励函数，观察策略如何变化。
- 记录一次 reward hacking 或失败模式。

这个项目的重点不是分数多高，而是理解“奖励定义什么，策略就会钻什么”。

## 参考项目

- [Gymnasium](https://gymnasium.farama.org/)：代表强化学习环境 API。先看 basic usage、training an agent、custom env。
- [CleanRL](https://github.com/vwxyzjn/cleanrl)：代表单文件可读算法实现。先看 PPO、DQN 的脚本和实验日志。
- [Stable-Baselines3](https://stable-baselines3.readthedocs.io/)：代表可靠 baseline。先看 examples、evaluation helper、RL Zoo。
- [RLlib](https://docs.ray.io/en/master/rllib/index.html)：代表分布式、多 agent 和生产级 RL。等需要规模化再看。
- [TRL](https://huggingface.co/docs/trl)：看 RL 如何进入语言模型 post-training。

更详细的信息源见 [[RL 信息来源]]。项目拆解见 [[RL 开源项目]]。工具分层见 [[RL 框架和工具]]。

## 工程实践清单

- 环境可复现，随机种子和版本记录清楚。
- 奖励函数写成可读代码，并记录设计理由。
- 训练曲线和评测曲线分开。
- 至少保存一次失败策略的视频或 trace。
- 评测不只看平均 reward，也看任务约束和安全边界。

## 必学知识

必须理解：

- agent/environment/action/reward/policy。
- exploration 和 exploitation。
- reward hacking。
- 离线 benchmark 和真实环境的差异。

必须能读代码：

- 一个 Gymnasium 环境。
- 一个 CleanRL 单文件算法。
- 一个 Stable-Baselines3 训练脚本。

知道存在即可：

- actor-critic 家族。
- offline RL。
- multi-agent RL。
- 大模型 RLHF/GRPO 的分布式训练细节。

## 阶段验收标准

- 能写出一个简单环境或修改一个现有环境。
- 能解释 reward 改动为什么改变策略。
- 能用曲线、视频或 trace 证明策略行为。
- 能指出 RLHF/DPO/GRPO 与传统 RL 的连接和差异。
- 能说明什么时候不该用 RL：反馈稀疏、环境不可控、评测不可靠时，先别上。

## 继续深入

传统 RL 先读 Sutton & Barto 和 Spinning Up。大模型后训练方向先读 [[Post-Train 路径]]，再看 TRL 和 OpenRLHF。两条线会相遇，但不要一开始混成一团。
