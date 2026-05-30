# Transformer

Transformer 是现在大多数大语言模型的骨架。它解决的不是“让机器理解语言”这种大问题，而是一个更具体的问题：给定一串 [[Token]]，模型怎样在同一段上下文里快速组合信息，并继续预测下一个 token。

早期循环神经网络按顺序读输入。句子越长，训练越慢，远距离信息也容易丢。Transformer 把一段文本同时放进模型，用 [[Attention]] 计算 token 之间的关系，再经过多层前馈网络和残差结构，把局部词义、语法关系、代码结构、格式习惯压进参数里。

## 核心机制

一个简化的 Transformer 解码模型可以这样看：

```text
text -> tokenizer -> token ids -> embeddings
     -> repeated transformer blocks
     -> logits over vocabulary
     -> sample next token
```

每个 block 里最重要的是两件事：

- self-attention：当前位置可以参考前面哪些 token。
- feed-forward network：对每个位置的表示做非线性变换。

大语言模型做 [[Inference]] 时通常是自回归的。它先看已有上下文，预测下一个 token，把这个 token 放回上下文，再继续预测。聊天、代码补全、工具调用参数生成，本质上都在走这个循环。

## 工程形态

应用开发者一般不会手写 Transformer，但会被它的工程后果影响：

- [[Context Window]] 有上限，因为 attention 和 KV cache 都要占显存。
- 输入输出按 [[Token]] 计费，不按汉字、单词或请求数计费。
- 首 token 延迟和总生成时间不同，前者受 prefill 影响，后者受 decode 影响。
- 长上下文并不等于模型会稳定使用所有信息，[[RAG]] 仍然需要排序、压缩和引用检查。
- 自部署模型时，batch、KV cache、量化和并发调度会直接决定吞吐。

做 [[Agent]] 时，Transformer 还会制造一个常见误会：模型看起来在“规划”，但工程上它仍是在上下文里逐步生成 token。真正的持久状态、工具权限、失败重试和审计日志不在模型内部，需要系统自己设计。

## 最小例子

下面的伪代码不是 Transformer 实现，只是说明推理循环为什么会不断变长：

```python
tokens = tokenize(prompt)

for _ in range(max_new_tokens):
    logits = model(tokens)
    next_token = sample(logits[-1])
    tokens.append(next_token)
    if next_token == end_token:
        break
```

真实系统会缓存已经算过的 key/value，避免每次从头处理完整上下文。这就是 KV cache。它提升生成速度，也吃显存，所以长对话和高并发会变贵。

## 边界和失败模式

不要把 Transformer 当成一个完整产品架构。它只负责从上下文到 token 的概率建模。

常见失败点包括：

- 上下文里塞太多资料，相关片段被噪声淹没。
- prompt 里依赖隐含规则，模型在长任务中忘记约束。
- 把 attention 权重当成可靠解释。它能提供线索，但不能证明模型“为什么这么想”。
- 只看模型参数规模，不看 tokenizer、数据、后训练、推理配置和系统约束。

在 [[AI Infra]] 或 [[Pre-Train]] 里，多头注意力、位置编码、layer norm、MoE、FlashAttention 和并行训练会继续展开。应用层更常遇到的是 token、上下文、推理循环和显存成本。

## 参考项目和资料

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：原始论文，结构图和第 3 节是核心位置。
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)：图解 Transformer 直觉。
- [Hugging Face Transformers](https://huggingface.co/docs/transformers)：看模型如何被加载、生成和微调。
- [nanoGPT](https://github.com/karpathy/nanoGPT)：紧凑的 GPT 训练实现，核心文件包括 `model.py` 和 `train.py`。
