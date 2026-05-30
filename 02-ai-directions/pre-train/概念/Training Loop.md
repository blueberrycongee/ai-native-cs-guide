# Training Loop

Training Loop 是模型训练的主循环。它把数据 batch 送进模型，计算 loss，反向传播，更新参数，记录日志，并定期保存 [[Checkpoint]]。

理解它，比先背训练平台名更重要。

## 工作机制

最小循环是：

```python
for batch in dataloader:
    logits = model(batch["input_ids"])
    loss = cross_entropy(logits, batch["labels"])
    loss.backward()
    optimizer.step()
    optimizer.zero_grad()
```

语言模型预训练常见目标是 next-token prediction。输入是一段 token，label 是右移一位的 token。模型不是背答案，而是在大量文本上学习“给定前文，下一个 token 的分布”。

## 工程形态

真实训练循环会多出：

- gradient accumulation
- mixed precision
- learning rate schedule
- gradient clipping
- evaluation steps
- checkpoint save/resume
- distributed communication
- logging

框架可以帮你处理这些，但你仍要知道它们在哪一步发生。

## 常见失败模式

- loss 降了，但生成样例很差。
- 训练和推理 tokenizer 不一致。
- batch 构造错位，label 对不上 input。
- 学习率太高导致 loss 爆炸。
- resume checkpoint 后 optimizer 或 scheduler 状态没恢复。

## 参考项目

- [nanoGPT](https://github.com/karpathy/nanoGPT)：读 `train.py`。
- [Hugging Face Trainer](https://huggingface.co/docs/transformers/trainer)：看框架如何封装训练循环。
- [[Dataset Pipeline]]
- [[Checkpoint]]
