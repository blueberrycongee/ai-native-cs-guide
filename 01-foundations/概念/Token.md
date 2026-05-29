# Token

Token 是模型读写文本的基本单位。它可以是一个字、一个词的一部分、一个标点，也可能是一段常见字符串。

理解 token 很重要，因为大模型的成本、上下文长度、延迟和很多奇怪输出，都和 token 有关。

相关概念：

- [[Embedding]]
- [[Context Window]]
- [[Inference]]
- [[LLM]]

## 学到什么程度

入门阶段需要知道：

- 模型不是直接处理“字”或“词”，而是处理 token
- 同一句话在不同 tokenizer 下可能切出不同 token
- 中文、代码、空格和特殊符号的 token 成本可能差异很大
- API 计费和上下文窗口通常都按 token 计算

不需要一开始研究每种分词算法。BPE、Unigram、SentencePiece 这些可以等你做 tokenizer、训练模型或压成本时再深入。

## 在项目里怎么出现

最常见的场景：

- prompt 明明不长，token 数却很高
- 代码、JSON、日志放进 prompt 后成本暴涨
- [[RAG]] 切块时要控制每块 token 数
- 流式输出时，前端看到的是模型逐步吐出的 token 或 token 片段

如果你做 [[Agent]]，token 还会影响 memory、工具结果压缩和长任务成本。

## 资料

- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)：用来直观看文本如何被切分。
- [Hugging Face Tokenizers](https://huggingface.co/docs/tokenizers)：适合了解 tokenizer 在工程里的角色。
