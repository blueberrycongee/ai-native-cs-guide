# Token

Token 是模型实际处理的文本单位。它可能是一个英文单词、一个中文字符、一个词的一部分，也可能是空格和标点。大语言模型不直接读字符串，它读 token id。

这件事看起来小，但会影响计费、上下文长度、流式输出、截断、缓存和多语言体验。

## 工作机制

tokenizer 把文本切成 token，再把 token 映射成整数 id。模型接收 id 序列，通过 [[Embedding]] 查表得到向量。

```text
"hello world" -> ["hello", " world"] -> [15339, 1917]
```

不同模型的 tokenizer 不一样。同一句中文、代码或 JSON，在不同模型里可能切出不同数量的 token。别用字符数估算成本，尤其别用中文字符数直接估算英文模型的上下文占用。

## 工程形态

Token 在 AI 项目里经常以这些方式出现：

- API 计费按输入 token 和输出 token 计算。
- [[Context Window]] 限制的是 token 数，不是字符数。
- [[Streaming Response]] 通常是按 token 或 token 片段逐步返回。
- JSON、代码和表格会产生很多结构 token，长工具参数尤其容易超限。
- prompt cache、KV cache 和截断策略都以 token 序列为基础。

做 [[RAG]] 时，切块大小也应该按 token 估算。按字符切块会在中英文混排、代码块和表格里出问题。

## 最小例子

一个后端在调用模型前通常会做预算：

```python
input_tokens = count_tokens(system_prompt + user_text + retrieved_context)
max_output_tokens = 800

if input_tokens + max_output_tokens > context_limit:
    retrieved_context = compress_or_drop(retrieved_context)
```

这里的重点不是精确复刻某家 API 的 tokenizer，而是别把上下文限制留到上游模型报错时才发现。

## 边界和失败模式

常见失败包括：

- 用字符数控制 prompt，结果线上偶发超上下文。
- 忽略输出 token 预算，模型生成到一半被截断。
- 把完整日志、HTML 或 PDF 原文塞进上下文，token 成本很快失控。
- 流式渲染时按字符处理，遇到半个 token、半个 Markdown 结构或半个 JSON 片段就显示异常。

Token 是模型和系统之间的接口单位。计费、上下文长度、缓存、流式输出和截断都建立在这层抽象上。

## 参考资料

- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)：直观看同一段文本如何被切分。
- [Hugging Face Tokenizers](https://huggingface.co/docs/tokenizers)：理解 tokenizer 的工程实现。
- [tiktoken](https://github.com/openai/tiktoken)：适合在 Python 项目里估算 token 数。
