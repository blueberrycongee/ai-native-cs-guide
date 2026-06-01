# GRPO

GRPO，全称 Group Relative Policy Optimization，是近年大模型后训练中常见的强化学习方法之一。它用一组候选回答的相对表现来更新策略，常出现在推理、数学、代码和可验证任务的训练讨论里。

初学时可以先理解工程位置；做 RL Infra 时，必须继续理解 old policy、importance sampling、buffer 和 eval。完整系统视角、公式推导、参数解释、buffer 字段列表和 DAPO/GSPO 对比见 [[LLM RL Infra]]。

## 工作机制

一个简化流程：

```text
prompt
  -> model samples a group of responses
  -> reward function scores each response
  -> compare responses within group
  -> update policy
```

和传统 RLHF 不同，GRPO 不一定需要单独 critic。它更依赖 reward 设计、采样和 rollout 系统。

## On-policy 和 old policy

GRPO 通常是 near-on-policy：用当前策略的冻结快照，也就是 old policy，生成一批 responses，然后用这些新样本更新当前 policy。公式里出现 old policy 不代表它就是 offline/off-policy replay。

真正的 off-policy 风险来自工程系统：rollout engine 权重同步滞后、异步训练复用旧样本、vLLM 推理策略和训练策略不一致，都会让行为策略和目标策略偏离。此时 importance sampling ratio、clipping、off-policy correction 和样本丢弃策略才变得关键。

## 核心公式（一行总结）

$$
\hat A_i = \frac{R_i - \operatorname{mean}(R_1,\ldots,R_G)}{\operatorname{std}(R_1,\ldots,R_G)}
$$

GRPO 用同一 prompt 下组内 reward 的归一化结果作为 advantage，省掉 critic，再用 PPO 风格的 clipped ratio 限制单次更新幅度，并加 KL 约束控制策略漂移。完整 objective、importance ratio 形式和参数解释见 [[LLM RL Infra#GRPO 公式速览]]。

GRPO 省掉 critic，不代表省掉系统复杂度。rollout buffer 仍要保存 prompt、response、old logprobs、ref logprobs、reward、advantage、mask、长度和 metadata。字段列表见 [[LLM RL Infra#GRPO batch 应该保存什么]]。

## 工程形态

GRPO 项目通常需要：

- 能批量生成候选回答的推理后端，例如 vLLM。
- reward function 或 verifier。
- 稳定的数据和 prompt 模板。
- 训练框架，例如 TRL 或 OpenRLHF。
- eval 区分训练 reward 和真实任务质量。

如果 reward 只检查答案格式，模型可能学会格式正确但内容错误。

## 参考工具

- [TRL GRPO docs](https://huggingface.co/docs/trl)
- [OpenRLHF RL training guide](https://openrlhf.readthedocs.io/en/latest/agent_training.html)
- [DeepSeekMath](https://arxiv.org/abs/2402.03300)
- [[LLM RL Infra]]
- [[RLHF]]
- [[Evals]]
