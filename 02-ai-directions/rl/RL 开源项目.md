# RL 开源项目

RL 项目可以按环境接口、单文件算法、baseline、分布式框架和语言模型后训练几类来看。

## Gymnasium

- GitHub：[Farama-Foundation/Gymnasium](https://github.com/Farama-Foundation/Gymnasium)
- Docs：[Gymnasium docs](https://gymnasium.farama.org/)
- 类型：RL 环境 API 和参考环境
- 代表形态：`reset`、`step`、observation、action、reward、terminated、truncated 这套环境接口。
- 相关主题：[[Environment]]、spaces、wrappers、vectorized env、自定义环境。

## CleanRL

- GitHub：[vwxyzjn/cleanrl](https://github.com/vwxyzjn/cleanrl)
- 类型：单文件 RL 算法实现
- 代表形态：把 PPO、DQN、SAC 等算法的 rollout、advantage、loss 和 update 放在一个脚本里。
- 相关主题：[[PPO]]、logging、evaluation、实验复现。

## Stable-Baselines3

- GitHub：[DLR-RM/stable-baselines3](https://github.com/DLR-RM/stable-baselines3)
- Docs：[Stable-Baselines3 docs](https://stable-baselines3.readthedocs.io/)
- 类型：RL baseline 实现
- 代表形态：训练、保存、加载、评测、callback、vector env 和 RL Zoo。
- 相关主题：PPO、DQN、SAC、Evaluation helper、RL Baselines3 Zoo。

## RLlib

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[RLlib docs](https://docs.ray.io/en/master/rllib/index.html)
- 类型：分布式、多 agent、生产级 RL 框架
- 代表形态：大规模 rollout、learner、env runner、多 agent 和 offline RL。
- 相关主题：[[AI Infra]]、分布式训练、multi-agent examples、offline RL examples。
