# GRPO

GRPO，全称 Group Relative Policy Optimization，是近年大模型后训练中常见的强化学习方法之一。它用一组候选回答的相对表现来更新策略，常出现在推理、数学、代码和可验证任务的训练讨论里。

初学时可以先理解工程位置；准备面试或做 RL Infra 时，必须继续理解 old policy、importance sampling、buffer 和 eval。详细系统视角见 [[LLM RL Infra]]。

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

## 核心公式

GRPO 常见 objective：

$$
J_{GRPO}(\theta)=
\mathbb{E}_{q,\{o_i\}\sim\pi_{\theta_{old}}}
\left[
\frac{1}{G}\sum_{i=1}^{G}\frac{1}{|o_i|}\sum_{t=1}^{|o_i|}
\min
\left(
r_{i,t}(\theta)\hat A_{i,t},
\operatorname{clip}(r_{i,t}(\theta),1-\epsilon,1+\epsilon)\hat A_{i,t}
\right)
-\beta D_{KL}(\pi_\theta || \pi_{ref})
\right]
$$

其中：

- $r_{i,t}(\theta)=\pi_\theta(o_{i,t}|q,o_{i,<t})/\pi_{\theta_{old}}(o_{i,t}|q,o_{i,<t})$ 是 importance sampling ratio。
- $\hat A_i=(R_i-\operatorname{mean}(R_1,\ldots,R_G))/\operatorname{std}(R_1,\ldots,R_G)$ 是组内相对 advantage。
- $\epsilon$ 限制单次更新幅度，$\beta$ 控制和参考模型的 KL 约束。

GRPO 省掉 critic，不代表省掉系统复杂度。rollout buffer 仍要保存 prompt、response、old logprobs、ref logprobs、reward、advantage、mask、长度和 metadata。

## 工程形态

GRPO 项目通常需要：

- 能批量生成候选回答的推理后端，例如 vLLM。
- reward function 或 verifier。
- 稳定的数据和 prompt 模板。
- 训练框架，例如 TRL 或 OpenRLHF。
- eval 区分训练 reward 和真实任务质量。

如果 reward 只检查答案格式，模型可能学会格式正确但内容错误。

## 常见失败模式

- reward 太窄，模型钻规则空子。
- 采样温度和候选数设置不当，训练信号差。
- rollout 成本高，实验不可持续。
- eval 和 reward 同源，结果虚高。
- 数学或代码任务只看最终答案，不看过程风险。

## 参考工具

- [TRL GRPO docs](https://huggingface.co/docs/trl)
- [OpenRLHF RL training guide](https://openrlhf.readthedocs.io/en/latest/agent_training.html)
- [DeepSeekMath](https://arxiv.org/abs/2402.03300)
- [[LLM RL Infra]]
- [[RL]]
- [[Evals]]
