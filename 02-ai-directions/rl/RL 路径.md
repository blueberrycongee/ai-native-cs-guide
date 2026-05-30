# RL 路径

RL 关注策略如何在环境反馈中改变行为。在 AI Native 语境里，它既包括 Gymnasium 这类传统环境，也包括大模型 post-training 里的 reward、verifier、rollout 和 eval。

## 交互链路

一个 RL 系统通常可以抽象成：

```text
policy
  -> action
  -> environment
  -> observation / reward
  -> trajectory
  -> update
  -> evaluation
```

相关概念包括 [[Environment]]、[[Policy]]、[[Reward]]、[[PPO]]、[[Offline RL]]、[[Reward Model]]、[[RLHF]] 和 [[GRPO]]。

## 系统组件

- 环境：状态、动作、终止条件、随机性和版本。
- 奖励：任务反馈、约束、稀疏性和 reward hacking。
- rollout：采样策略、轨迹保存、优势估计和样本复用。
- 训练：policy update、actor-critic、PPO/GRPO、importance sampling。
- 评测：平均 reward、失败轨迹、约束违反、任务成功率和稳定性。
- LLM RL Infra：actor、reference model、reward/verifier、rollout engine、sequence packing 和分布式调度。

## 代表项目

- [Gymnasium](https://gymnasium.farama.org/)：强化学习环境 API。
- [CleanRL](https://github.com/vwxyzjn/cleanrl)：单文件算法实现，相关主题包括 rollout、advantage、loss 和实验日志。
- [Stable-Baselines3](https://stable-baselines3.readthedocs.io/)：常用 RL baseline 工具。
- [RLlib](https://docs.ray.io/en/master/rllib/index.html)：分布式、多 agent 和生产级 RL 框架。
- [TRL](https://huggingface.co/docs/trl)：语言模型 post-training 中的 RL 路径。

资料入口见 [[RL 信息来源]]，项目入口见 [[RL 开源项目]]，工具生态见 [[RL 框架和工具]]。
