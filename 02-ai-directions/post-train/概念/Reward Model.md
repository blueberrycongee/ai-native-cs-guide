# Reward Model

Reward Model 是把回答映射成奖励分数的模型。它常用于 [[RLHF]]，也可以帮助训练或筛选候选回答。

它不是客观真理，只是从偏好数据中学出来的评分器。

## 工作机制

常见训练数据是：

```text
prompt
chosen response
rejected response
```

reward model 学习给 chosen 更高分，给 rejected 更低分。训练好后，它可以给新回答打分，作为 RL 阶段的反馈。

## 工程形态

Reward model 项目要关注：

- 偏好数据覆盖哪些任务。
- 标注标准是否一致。
- reward 分数是否和真实用户质量相关。
- 是否会被 policy 利用漏洞。
- 是否需要过程奖励，而不是只看最终回答。

在很多小项目里，不需要先训练 reward model。SFT 或 DPO 已经足够暴露大部分问题。

## 参考资料

- [InstructGPT paper](https://arxiv.org/abs/2203.02155)
- [TRL reward modeling docs](https://huggingface.co/docs/trl)
- [[Preference Optimization]]
- [[RLHF]]
