# DPO

DPO，全称 Direct Preference Optimization，是一种用偏好数据直接优化模型的方法。它常被用来替代更复杂的 RLHF pipeline。

DPO 的工程吸引力在于：你不需要单独训练 reward model，也不需要跑完整 PPO 循环，就能用 chosen/rejected 数据调整模型偏好。

## 工作机制

DPO 输入通常是：

```text
prompt
chosen response
rejected response
reference model
policy model
```

训练会鼓励 policy model 相对 reference model 更偏向 chosen，而不是 rejected。

## 工程形态

DPO 项目要关注：

- preference pairs 的质量。
- reference model 是否和初始 policy 一致。
- beta 等超参数。
- 训练后是否过度偏向某种风格。
- 与 SFT 模型的对比。

DPO 不是万能"对齐按钮"。如果 chosen 本身事实错误，模型会更稳定地学错。

## 参考工具

- [TRL DPOTrainer](https://huggingface.co/docs/trl)
- [Direct Preference Optimization paper](https://arxiv.org/abs/2305.18290)
- [Axolotl DPO docs](https://docs.axolotl.ai/)
