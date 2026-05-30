# RL 开源项目

RL 项目可以按环境接口、单文件算法、baseline、分布式框架和语言模型后训练几类来看。

## Gymnasium

- GitHub：[Farama-Foundation/Gymnasium](https://github.com/Farama-Foundation/Gymnasium)
- Docs：[Gymnasium docs](https://gymnasium.farama.org/)

Gymnasium 是 RL 环境 API 和参考环境。`reset`、`step`、observation、action、reward、terminated、truncated 这套接口，以及 [[Environment]]、spaces、wrappers、vectorized env、自定义环境，都在这个生态里展开。

## CleanRL

- GitHub：[vwxyzjn/cleanrl](https://github.com/vwxyzjn/cleanrl)

CleanRL 是单文件 RL 算法实现。PPO、DQN、SAC 等算法的 rollout、advantage、loss、update、logging、evaluation 和实验复现都集中在较短的脚本里。

## Stable-Baselines3

- GitHub：[DLR-RM/stable-baselines3](https://github.com/DLR-RM/stable-baselines3)
- Docs：[Stable-Baselines3 docs](https://stable-baselines3.readthedocs.io/)

Stable-Baselines3 是常见 RL baseline 实现，覆盖训练、保存、加载、评测、callback、vector env 和 RL Zoo，也包括 PPO、DQN、SAC、Evaluation helper 等常用接口。

## RLlib

- GitHub：[ray-project/ray](https://github.com/ray-project/ray)
- Docs：[RLlib docs](https://docs.ray.io/en/master/rllib/index.html)

RLlib 是分布式、多 agent、生产级 RL 框架。大规模 rollout、learner、env runner、多 agent、offline RL、[[AI Infra]]、分布式训练、multi-agent examples 和 offline RL examples 都会在这里出现。
