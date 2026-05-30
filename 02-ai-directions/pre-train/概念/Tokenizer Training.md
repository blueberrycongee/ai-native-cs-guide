# Tokenizer Training

Tokenizer Training 是从语料中学习如何把文本切成 [[Token]] 的过程。tokenizer 决定模型看到什么单位，也影响上下文长度、训练效率、多语言表现和代码处理能力。

## 核心问题

模型不直接读字符串。它读 token id。tokenizer 把文本切分并映射到 id，再通过 [[Embedding]] 进入模型。

如果 tokenizer 对中文、代码、数学符号或领域术语切得很差，模型训练和推理都会受影响。

## 工程形态

训练 tokenizer 通常要决定：

- 语料范围。
- vocabulary size。
- normalizer 和 pre-tokenizer。
- special tokens。
- 是否保留空格、换行和代码符号。

LLM 常用 BPE、Unigram 或 SentencePiece 一类方法。项目里不需要从零实现算法，但要知道 tokenizer 是训练产物，不是随便选的字符串函数。

## 常见失败模式

- 训练和推理使用不同 tokenizer。
- 新增 special token 后没有正确 resize embedding。
- 中英文混合语料切分效率差，context window 被浪费。
- 代码语料里空格、缩进和换行处理不当。
- tokenizer 文件没有版本管理。

## 最小实验

用同一段中文、英文和代码，对比两个 tokenizer 的 token 数和切分结果。然后估算同一上下文在不同模型里的成本差异。

这个练习能直接连接 [[Token]]、[[Context Window]] 和训练成本。

## 参考资料

- [Hugging Face Tokenizers](https://huggingface.co/docs/tokenizers)
- [SentencePiece](https://github.com/google/sentencepiece)
- [tiktoken](https://github.com/openai/tiktoken)
