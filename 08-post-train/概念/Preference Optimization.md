# Preference Optimization

Preference Optimization 用偏好数据训练模型。偏好数据通常不是“标准答案”，而是同一个输入下两个或多个回答的比较：哪个更好，为什么更好。

它解决的问题是：很多任务没有唯一正确答案，但有明显更符合人类偏好的回答。

## 工作机制

偏好样例通常长这样：

```json
{
  "prompt": "...",
  "chosen": "更好的回答",
  "rejected": "较差的回答"
}
```

训练目标让模型更倾向 chosen，远离 rejected。[[DPO]] 是常见方法之一。[[RLHF]] 也使用偏好信号，但 pipeline 更复杂。

## 工程形态

偏好优化项目要先定义“好”：

- 更准确。
- 更遵循格式。
- 更少废话。
- 更安全。
- 更会拒答。
- 更适合工具调用。

如果标注规则不清，模型会学到混乱偏好。

## 常见失败模式

- chosen/rejected 差异太小，信号弱。
- 偏好只优化风格，事实正确性下降。
- 标注者标准不一致。
- 数据里混入安全策略和任务质量，但没有分开评测。
- 用偏好优化解决本该由产品逻辑解决的问题。

## 参考资料

- [DPO paper](https://arxiv.org/abs/2305.18290)
- [TRL DPOTrainer](https://huggingface.co/docs/trl)
- [[DPO]]
- [[Reward Model]]
