# RL 框架和工具

RL 工具可以按环境、算法实现、baseline、分布式训练和大模型后训练来整理。

## 环境

- Gymnasium：强化学习环境 API、自定义环境、状态、动作和终止条件。
- PettingZoo：多 agent 环境接口。
- Brax、MuJoCo、Isaac Gym：物理仿真环境。
- OpenSpiel：博弈和多 agent 研究环境。

## 算法实现和 baseline

- CleanRL：单文件算法实现，相关主题包括 rollout、advantage、loss 和实验日志。
- Stable-Baselines3：常用 baseline、训练、保存、加载和评测。
- CORL：offline RL 单文件实现。

## 分布式和生产形态

- RLlib：env runner、learner、多 agent、offline RL 和分布式训练。
- TensorBoard / Weights & Biases：reward、loss、episode length 和 eval metrics。

## 大模型 RL

- TRL：语言模型 post-training 中的 PPO、GRPO 和 reward model。
- OpenRLHF：RLHF/GRPO、Ray、vLLM 和分布式训练。
- verl：actor、rollout、reference、reward worker、GRPO/PPO trainer、vLLM/SGLang rollout、remove padding 和 sequence parallel。

相关系统视角见 [[LLM RL Infra]]。
