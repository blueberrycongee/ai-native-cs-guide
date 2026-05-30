# Checkpoint

Checkpoint 是训练过程中保存的模型状态。它让训练可以恢复，也让你能比较不同阶段的模型行为。

一个 checkpoint 不只是模型权重。完整恢复训练通常还需要 optimizer、scheduler、随机数状态、训练步数和配置。

## 工作机制

常见内容包括：

```text
model weights
optimizer state
lr scheduler state
global step
random states
training config
tokenizer
```

只保存权重可以用于推理，但不一定能无缝恢复训练。

## 工程形态

训练实验要记录：

- checkpoint 保存频率。
- 保留几个历史版本。
- 最佳 checkpoint 如何选择。
- 是否能从中断处恢复。
- checkpoint 对应的数据版本和代码版本。

大模型训练里，checkpoint 还涉及分片、存储带宽、跨节点恢复和格式转换。

## 常见失败模式

- 只保存模型权重，训练中断后 optimizer 状态丢失。
- tokenizer 没和 checkpoint 一起保存。
- checkpoint 太频繁，存储爆掉。
- checkpoint 太少，失败后回滚太远。
- 没记录代码版本，几周后无法加载。

## 参考项目

- [nanoGPT](https://github.com/karpathy/nanoGPT)：看简单 checkpoint 保存和恢复。
- [LitGPT](https://github.com/Lightning-AI/litgpt)：看更完整的 checkpoint 和格式转换。
- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)：看大规模训练 checkpoint 问题。
