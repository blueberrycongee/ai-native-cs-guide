# RLHF

RLHF，全称 Reinforcement Learning from Human Feedback，是用人类反馈训练模型行为的一类方法。经典 pipeline 通常包括 SFT、reward model 和基于奖励的策略优化。

它的目标不是让模型获得所有知识，而是让模型输出更符合人类偏好和产品约束。

## 工作机制

经典流程：

```text
pretrained model
  -> SFT
  -> collect preference data
  -> train reward model
  -> optimize policy with RL
  -> evaluate
```

这里的 RL 通常需要 reference model、policy model、reward model，有时还需要 critic。工程复杂度明显高于 SFT 和 DPO。

## 工程形态

真实 RLHF 项目难点在：

- 偏好数据采集。
- reward model 是否可靠。
- rollout 生成成本。
- 多模型资源调度。
- reward hacking。
- 安全和任务 eval。

这也是 OpenRLHF 这类框架要引入 Ray、vLLM、DeepSpeed 的原因。

## 参考资料

- [InstructGPT paper](https://arxiv.org/abs/2203.02155)
- [OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)
- [[Reward Model]]
- [[RL]]
