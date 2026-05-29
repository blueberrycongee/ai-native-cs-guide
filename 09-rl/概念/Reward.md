# Reward

Reward 是环境给策略的反馈。它告诉训练过程哪些行为应该被强化。RL 的很多难点都藏在 reward 里。

奖励写得不对，模型会认真学错。

## 工作机制

在每一步交互后，环境返回一个 reward。算法优化的是长期累计回报，不只是当前一步的分数。

```text
observation -> action -> reward -> next observation
```

reward 可以稀疏，比如任务完成才给分；也可以密集，比如每接近目标一点给分。两者都有问题：稀疏奖励难学，密集奖励容易引入错误捷径。

## 工程形态

设计 reward 时要写清：

- 奖励对应的真实目标是什么。
- 哪些行为应该被惩罚。
- 是否存在钻空子的路径。
- 训练 reward 和 eval 指标是否分开。
- 人工检查哪些高风险样例。

大模型后训练里的 reward function、verifier、reward model，本质上也在处理这个问题。

## 常见失败模式

- reward hacking：策略学会刷分，不做真实任务。
- 只奖励最终答案，忽略危险过程。
- reward 太稀疏，训练没有信号。
- reward 和 eval 同源，分数虚高。
- 人类偏好标注标准不一致。

## 参考资料

- [InstructGPT paper](https://arxiv.org/abs/2203.02155)
- [[Reward Model]]
- [[GRPO]]
