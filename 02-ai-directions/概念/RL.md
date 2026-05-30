# RL

RL，也就是强化学习，研究智能体如何在环境里通过反馈学习策略。在这个仓库里，RL 不从算法清单讲起，而是放在 AI Native 语境里看：环境、动作、奖励和评测如何塑造行为。

方向总览见 [[RL 方向]]。

## 方向边界

RL 的核心不是“有一个奖励分数”，而是定义一个可交互环境，让策略通过反馈改变行为。

基本元素包括：

- agent：做决策的策略。
- environment：策略互动的世界。
- action：策略能做的动作。
- reward：环境给出的反馈。
- trajectory：一段交互历史。

大模型里的 RLHF、GRPO、工具环境训练和代码任务强化学习，都可以放进这个框架里看，但实现细节差别很大。

## 典型工程形态

常见形态包括：

- Gymnasium 环境里的传统 RL。
- CleanRL 或 Stable-Baselines3 训练 baseline。
- RLHF/GRPO 中用偏好、reward model 或任务结果优化语言模型。
- Agent 在代码、网页或工具环境里通过任务成功信号改进行为。

RL 的工程难点通常是奖励设计和评测。奖励写歪了，模型会钻空子。

## 和其他方向的关系

- 和 [[Post-Train]] 在 RLHF/GRPO 处交叉。
- 和 [[Agent]] 在环境交互和任务反馈处交叉。
- 依赖 [[Evals]] 判断是否真的学到了任务能力。
- 依赖 [[AI Infra]] 支撑大规模训练和 rollout。

## 相关页面

- [[RL 方向]]
- [[RL 信息来源]]
- [[RL 开源项目]]
- [[RL 框架和工具]]
- [[Environment]]
- [[Policy]]
- [[Reward]]
