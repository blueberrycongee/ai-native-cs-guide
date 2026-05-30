# Attention Is All You Need

原文：[Attention Is All You Need](https://arxiv.org/abs/1706.03762)

这篇论文的价值不只是提出了 Transformer。它把序列建模从“按时间步递归处理”推进到“在同一层里并行读取整段上下文”，让后来的大规模语言模型训练、长上下文推理和高吞吐服务都站在了一个新的结构基础上。

如果你只想理解今天的大语言模型，先不要从完整公式推导开始。先抓住一个问题：给定一串 [[Token]]，模型如何让每个位置高效读取其他位置的信息，并继续预测下一个 token。

## 论文解决的问题

在 Transformer 之前，序列模型常用 RNN、LSTM、GRU 或卷积结构。它们能处理序列，但有几个限制：

- 长距离依赖难学。
- 训练并行度差，尤其是按时间步递归的模型。
- 模型需要在局部窗口或隐藏状态里传递信息，信息路径不够直接。

论文提出的核心判断是：序列建模不一定要靠 recurrence。只要能让每个位置根据上下文动态读取信息，模型就可以在更高并行度下学习序列关系。

这就是 [[Attention]] 在 Transformer 里的作用。

## 关键抽象

Transformer 的关键抽象可以分成四层：

- token 表示：每个 token 先变成向量，并加入位置信息。
- self-attention：每个位置用 query 去匹配其他位置的 key，再加权读取 value。
- multi-head attention：并行做多组 attention，让模型在不同子空间里读取关系。
- feed-forward block：对每个位置的表示做非线性变换，再通过残差和归一化稳定训练。

简化的数据流是：

```text
tokens
  -> embeddings + positional encoding
  -> self-attention
  -> feed-forward network
  -> repeated blocks
  -> output probabilities
```

今天常见的 decoder-only LLM 可以看成 Transformer 思想的一条延伸路线：只保留自回归生成所需的结构，用 causal mask 防止当前位置看到未来 token。

## 今天仍然影响哪些系统

这篇论文直接连接到本仓库的多个核心概念：

- [[Transformer]]：模型骨架。
- [[Attention]]：上下文内信息读取机制。
- [[Context Window]]：attention 和 KV cache 让上下文长度变成成本问题。
- [[Inference]]：自回归 decode 让首 token 延迟和生成吞吐分开。
- [[Inference Serving]]：KV cache、batching、分页和调度都来自 Transformer 推理形态。
- [[Pre-Train]]：大规模 next-token prediction 依赖可并行训练的 Transformer block。

应用开发者不需要手写 Transformer，但必须理解它带来的工程后果：

- 输入输出按 token 计费。
- 长上下文会增加 prefill 成本。
- 多轮对话和 Agent trace 会吃掉 context window。
- RAG 不是把资料塞进 prompt 就结束，排序、压缩和引用检查仍然关键。

## 先读论文哪里

第一次读可以按这个顺序：

1. Abstract 和 Introduction：确认论文为什么要去掉 recurrence。
2. Figure 1：看 encoder、decoder、attention 和 feed-forward block 的整体结构。
3. Section 3.2：理解 scaled dot-product attention 和 multi-head attention。
4. Section 3.4、3.5：理解 position-wise feed-forward 和 positional encoding。
5. Section 5：看实验设置和训练成本，不要只看 BLEU 分数。

不建议第一次就卡在所有公式细节上。公式要服务于结构理解：query、key、value 分别承担什么角色，mask 为什么必要，多头为什么不是简单重复。

## 最容易误读的地方

第一，attention 不是可靠解释工具。某个 token 权重大，只能说明这次计算中它参与较多，不能直接证明模型“为什么这么想”。

第二，Transformer 不等于 ChatGPT。论文解决的是序列建模结构问题，不包含今天聊天模型里的 instruction tuning、RLHF、工具调用、安全策略和系统编排。

第三，长上下文不是免费能力。attention 结构让模型能在上下文内读信息，但工程上仍要付出显存、延迟和检索质量成本。

第四，原论文是 encoder-decoder 架构，今天很多 LLM 是 decoder-only。读论文时要区分原始机器翻译设置和现代自回归语言模型设置。

## 用代码验证理解

最小验证路径：

1. 读 [[Attention]] 和 [[Transformer]]。
2. 打开 [nanoGPT](https://github.com/karpathy/nanoGPT)，先看 `model.py` 里的 `CausalSelfAttention` 和 `Block`。
3. 看 `train.py` 里 batch 如何进入模型、loss 如何计算。
4. 改一次 context length，观察训练显存、速度和生成效果变化。

你应该能解释：

- 为什么 decoder-only 模型需要 causal mask。
- 为什么生成阶段不能一次性并行生成所有 token。
- KV cache 为什么能加速 decode，但会增加显存占用。
- 为什么 RAG 失败时不能只怪模型，而要检查检索、排序和上下文拼接。

## 和相邻论文的关系

- BERT：同样基于 Transformer，但走 encoder-only 和 mask language modeling 路线，更适合表示学习和理解任务。
- GPT 系列：把 Transformer decoder 用在自回归语言建模上，连接到今天的 [[Prompting]]、chat completion 和代码生成。
- FlashAttention：不改变 attention 的数学定义，重点优化 attention 计算的显存读写。
- PagedAttention：不改变模型结构，重点解决 LLM serving 里的 KV cache 管理。

这篇论文适合作为论文深挖的第一篇，因为它同时连接模型结构、训练并行、推理成本和系统设计。读懂它，不代表读懂了现代 LLM；但没读懂它，后面的 pre-train、post-train、inference serving 和 Agent 工程都会悬空。
