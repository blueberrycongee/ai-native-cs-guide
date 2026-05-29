# RL 信息来源

RL 的信息源要分层。教材讲概念，Gymnasium 讲环境接口，CleanRL 讲算法实现，Stable-Baselines3 讲可靠 baseline，RLlib 讲分布式和多 agent。

## 教材和课程

- [Reinforcement Learning: An Introduction](http://incompleteideas.net/book/the-book-2nd.html)：经典教材。先看 agent、environment、return、value、policy。
- [OpenAI Spinning Up](https://spinningup.openai.com/)：适合建立传统 RL 算法地图。

这些资料系统，但不一定直接回答大模型后训练问题。先用它们打底。

## 官方文档

- [Gymnasium](https://gymnasium.farama.org/)：看环境 API、spaces、wrappers、custom environment。
- [Stable-Baselines3](https://stable-baselines3.readthedocs.io/)：看可靠算法实现、evaluation helper、RL Zoo。
- [CleanRL](https://github.com/vwxyzjn/cleanrl)：看单文件算法实现。
- [RLlib](https://docs.ray.io/en/master/rllib/index.html)：看分布式、多 agent、offline RL 和生产级训练。

## 论文和项目

- [CleanRL paper](https://arxiv.org/abs/2111.08819)：理解为什么单文件实现对研究和学习有价值。
- [Gymnasium paper](https://arxiv.org/abs/2407.17032)：理解环境接口标准化问题。
- [RLlib paper](https://arxiv.org/abs/1712.09381)：看分布式 RL 抽象。
- [InstructGPT](https://arxiv.org/abs/2203.02155)：看 RLHF 如何进入语言模型。

## 社区讨论

社区讨论适合看真实坑：reward hacking、seed 不稳定、环境版本变化、benchmark 不可复现、RLlib 抽象复杂。结论要回到代码和实验，别照搬。
