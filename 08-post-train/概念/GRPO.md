# GRPO

GRPO，全称 Group Relative Policy Optimization，是近年大模型后训练中常见的强化学习方法之一。它用一组候选回答的相对表现来更新策略，常出现在推理、数学、代码和可验证任务的训练讨论里。

这里先理解工程位置，不急着推公式。

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
- [[RL]]
- [[Evals]]
