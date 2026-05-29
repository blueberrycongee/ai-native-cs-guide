# Pre-Train

这条路径关注模型预训练。它不是本仓库的第一实践路线，但需要有入口，避免读者把所有模型能力都归因于 prompt。

先读 [[Pre-Train]] 方向概念。

## 学习顺序

建议顺序：

1. 理解 [[Transformer]]、[[Token]] 和 [[Embedding]]
2. 用 nanoGPT 这类小项目看训练循环
3. 了解数据集、tokenizer、loss 和 evaluation
4. 再看分布式训练和训练稳定性

不要从大模型训练平台开始。没有小规模训练直觉时，平台细节很容易变成术语堆积。

## 入门项目

可以做一个很小的字符级或 token 级语言模型训练实验：

- 准备一个小文本数据集
- 训练一个小模型
- 观察 loss 和生成质量
- 改变数据和上下文长度，比较结果

它不会让你“训练出大模型”，但能帮你理解预训练到底在优化什么。

## 资料

- [nanoGPT](https://github.com/karpathy/nanoGPT)
- [The Pile](https://arxiv.org/abs/2101.00027)
- [GPT-3 paper](https://arxiv.org/abs/2005.14165)
