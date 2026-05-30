# RL 开源项目

RL 项目不要按“算法多不多”排序。入门先看能解释机制的项目，再看可靠 baseline 和分布式框架。

## Gymnasium

- GitHub：[Farama-Foundation/Gymnasium](https://github.com/Farama-Foundation/Gymnasium)
- Docs：[Gymnasium docs](https://gymnasium.farama.org/)
- 类型：RL 环境 API 和参考环境

学习价值：

- 理解 `reset`、`step`、observation、action、reward、terminated、truncated。
- 学会写自定义 [[Environment]]。
- 理解 wrappers 和 vectorized env。

先看：

- Basic usage。
- Training an agent。
- Create a custom environment。
- Spaces。

## CleanRL

- GitHub：[vwxyzjn/cleanrl](https://github.com/vwxyzjn/cleanrl)
- 类型：单文件 RL 算法实现

学习价值：

- 算法细节都在一个脚本里，适合读 [[PPO]]、DQN、SAC。
- 比大型框架更容易追踪 rollout、advantage、loss 和 update。

先看：

- `cleanrl/ppo.py` 或类似基础脚本。
- docs 中的 experiment 页面。
- logging 和 evaluation 部分。

## Stable-Baselines3

- GitHub：[DLR-RM/stable-baselines3](https://github.com/DLR-RM/stable-baselines3)
- Docs：[Stable-Baselines3 docs](https://stable-baselines3.readthedocs.io/)
- 类型：可靠 RL baseline 实现

学习价值：

- 快速训练 baseline。
- 评测、保存、加载、callback、vector env 较完整。
- 适合做项目验证，不必自己实现算法。

先看：

- examples。
- PPO、DQN、SAC 文档。
- Evaluation helper。
- RL Baselines3 Zoo。

## RLlib

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[RLlib docs](https://docs.ray.io/en/master/rllib/index.html)
- 类型：分布式、多 agent、生产级 RL 框架

学习价值：

- 理解大规模 rollout、learner、env runner、多 agent、offline RL。
- 适合后期连接 [[AI Infra]]。

先看：

- Getting started。
- Key concepts。
- Multi-agent examples。
- Offline RL examples。

入门不要从 RLlib 开始。它解决的是规模和复杂系统问题，不是最小概念问题。
