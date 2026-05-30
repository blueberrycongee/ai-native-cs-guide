# RL 信息来源

RL 的资料入口分成教材、环境接口、算法实现、baseline、分布式框架和大模型 RL Infra。

## 教材和课程

- [Reinforcement Learning: An Introduction](http://incompleteideas.net/book/the-book-2nd.html)：agent、environment、return、value、policy 等基础概念。
- [OpenAI Spinning Up](https://spinningup.openai.com/)：传统 RL 算法地图。

## 官方文档

- [Gymnasium](https://gymnasium.farama.org/)：环境 API、spaces、wrappers、custom environment。
- [Stable-Baselines3](https://stable-baselines3.readthedocs.io/)：baseline 算法、evaluation helper、RL Zoo。
- [CleanRL](https://github.com/vwxyzjn/cleanrl)：单文件算法实现。
- [RLlib](https://docs.ray.io/en/master/rllib/index.html)：分布式、多 agent、offline RL 和生产级训练。

## 论文和项目

- [CleanRL paper](https://arxiv.org/abs/2111.08819)：单文件实现对研究和复现的价值。
- [Gymnasium paper](https://arxiv.org/abs/2407.17032)：环境接口标准化。
- [RLlib paper](https://arxiv.org/abs/1712.09381)：分布式 RL 抽象。
- [InstructGPT](https://arxiv.org/abs/2203.02155)：RLHF 进入语言模型。
- [DeepSeekMath](https://arxiv.org/abs/2402.03300)：[[GRPO]] 的 critic-free group baseline 和公式。
- [DAPO](https://dapo-sia.github.io/static/pdf/dapo_paper.pdf)：长 CoT RLVR 中的 Clip-Higher、Dynamic Sampling、Token-Level Loss 和 Overlong Reward Shaping。
- [GSPO](https://arxiv.org/abs/2507.18071)：sequence-level importance ratio 与长序列、MoE RL 稳定性。
- [R3 / Rollout Routing Replay](https://arxiv.org/abs/2510.11370)：MoE RL 中训练和推理 router 不一致的问题。

## LLM RL Infra

- [verl](https://github.com/verl-project/verl)：actor、rollout、reference、reward worker、vLLM/SGLang、FSDP/Megatron 接入。
- [TRL GRPOTrainer](https://huggingface.co/docs/trl/grpo_trainer)：GRPO 配置、vLLM 生成、importance sampling、off-policy mismatch。
- [OpenRLHF RL Training Guide](https://openrlhf.readthedocs.io/en/latest/agent_training.html)：PPO/GRPO/RLOO、异步 rollout、off-policy correction、dynamic sampling 和 entropy。
- [NeMo RL GRPO Guide](https://docs.nvidia.com/nemo/rl/nightly/guides/grpo.html)：GRPO 工程 walkthrough 和 importance sampling correction。
- [[LLM RL Infra]]

## 社区问题

社区讨论里经常出现 reward hacking、seed 不稳定、环境版本变化、benchmark 不可复现和 RLlib 抽象复杂等问题。
