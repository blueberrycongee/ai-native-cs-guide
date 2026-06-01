# Attention

Attention 解决的问题很具体：模型处理某个 token 时，应该从上下文里的哪些位置取信息。

在 [[Transformer]] 里，attention 不是人类注意力的比喻，而是一套可微分的加权读取机制。每个位置生成 query、key、value。query 和 key 算相似度，相似度变成权重，再用权重加权 value。这样当前位置就能从上下文里拿到一份“混合后的信息”。

## 工作机制

简化后可以这样理解：

```text
当前 token 的 query
  与上下文 token 的 key 做匹配
  得到权重
  用权重加权上下文 token 的 value
  得到当前位置的新表示
```

## 基本公式

在 self-attention 里，输入是一段 token 的 hidden states，记作：

$$
X \in \mathbb{R}^{n \times d_{\text{model}}}
$$

其中 \(n\) 是序列长度，\(d_{\text{model}}\) 是每个 token 表示的维度。模型会把同一份 \(X\) 投影成 query、key、value：

$$
Q = XW_Q,\quad K = XW_K,\quad V = XW_V
$$

scaled dot-product attention 的核心公式是：

$$
\operatorname{Attention}(Q, K, V) =
\operatorname{softmax}\left(\frac{QK^\top}{\sqrt{d_k}} + M\right)V
$$

这里 \(QK^\top\) 得到每个 query 对所有 key 的相似度，\(\sqrt{d_k}\) 用来缩放分数，避免维度变大后 softmax 过于尖锐。\(M\) 是可选 mask；在解码模型里，causal mask 会把未来 token 的位置设成 \(-\infty\)，让当前位置只能看见自己和过去。

从单个位置 \(i\) 看，就是：

$$
\alpha_i =
\operatorname{softmax}\left(\frac{q_iK^\top}{\sqrt{d_k}} + m_i\right),
\quad
o_i = \alpha_iV
$$

\(\alpha_i\) 是当前位置对上下文各位置的权重，\(o_i\) 是加权读取 value 之后的新表示。

多头注意力把这个过程并行做多次：

$$
\operatorname{head}_r =
\operatorname{Attention}(XW_r^Q, XW_r^K, XW_r^V)
$$

$$
\operatorname{MultiHead}(X) =
\operatorname{Concat}(\operatorname{head}_1,\ldots,\operatorname{head}_h)W_O
$$

不同 head 可以学习不同关系，但不要急着把某个 head 解释成“语法 head”或“事实 head”。真实模型里的行为没有这么整齐。

## 工程形态

公式之外，Attention 还会直接影响系统工程：

- 上下文变长，prefill 阶段会变慢。
- KV cache 让 decode 更快，但会随序列长度和并发数占显存。
- [[RAG]] 检索结果排序差时，模型可能把错误片段当成主要依据。
- 长文档任务需要切块、摘要、重排或多轮检索，不能只靠“放进上下文”。
- 推理框架会用 FlashAttention、PagedAttention 等技术降低显存和调度压力。

这也是为什么 [[Context Window]] 大不等于系统可靠。模型能看到，不代表它会稳定用对。

## 最小例子

下面是注意力的概念版，不处理 batch、多头、mask 和数值稳定：

```python
scores = (query @ keys.T) / sqrt(d_k)
weights = softmax(scores)
output = weights @ values
```

解码模型还会加 causal mask，防止当前位置看到未来 token。没有这个约束，训练和生成就不是同一个问题。

## 边界和失败模式

Attention 容易被误用成解释工具。看到某些 token 权重大，只能说明这次计算里它们参与较多，不能直接证明模型的推理理由。

常见工程失败包括：

- 检索片段过长，真正有用的句子被稀释。
- 多份资料互相冲突，模型引用了排序靠前但并不可信的内容。
- 长上下文成本失控，却没有缓存、裁剪和分层摘要策略。
- 调试幻觉时只改 prompt，不检查检索、排序、引用和上下文拼接。

在 Agent 或 RAG 项目里，attention 的影响主要落在上下文设计；到 [[AI Infra]] 层面才会涉及 FlashAttention、KV cache 分页和调度。

## 参考资料

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：第 3 节是原始结构。
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)：用图解释 query、key、value。
- [vLLM paper](https://arxiv.org/abs/2309.06180)：从推理系统角度看 KV cache 管理。
