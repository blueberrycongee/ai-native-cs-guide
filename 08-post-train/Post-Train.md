# Post-Train

这条路径关注预训练之后的模型改造：指令微调、偏好优化、对齐、工具使用和评测。

先读 [[Post-Train]] 方向概念。

## 学习顺序

建议先理解：

- [[Pre-Train]] 和 post-train 的区别
- 指令数据为什么影响模型可用性
- 偏好数据如何影响输出风格和安全边界
- [[Evals]] 如何判断训练是否有效

公式可以晚一点看。先知道每种方法想修什么问题。

## 入门项目

可以做一个小模型指令微调实验：

1. 选一个小模型
2. 准备一小批任务样例
3. 微调前后跑同一组 eval
4. 记录哪些能力变好，哪些退化

不要只看训练 loss。用户真正关心的是任务是否更稳，格式是否更可控，错误是否更少。

## 资料

- [InstructGPT paper](https://arxiv.org/abs/2203.02155)
- [Direct Preference Optimization](https://arxiv.org/abs/2305.18290)
- [TRL GitHub](https://github.com/huggingface/trl)
