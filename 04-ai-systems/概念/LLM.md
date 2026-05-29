# LLM

LLM，也就是大语言模型，是能基于上下文生成文本、代码或结构化输出的模型。它通常基于 [[Transformer]]，输入输出按 [[Token]] 处理。

对这个仓库来说，LLM 不是一个孤立概念。它是 [[Agent]]、[[RAG]]、[[Prompting]]、[[Context Engineering]] 和 [[Inference]] 的共同基础。

## 学到什么程度

入门阶段需要知道：

- LLM 根据上下文生成下一个 token，不是数据库查询
- 它会受 [[Context Window]]、采样参数和系统提示影响
- 它可能编造内容，需要检索、校验或评测
- 它可以调用工具，但工具权限和结果校验要由系统设计保证

不必一开始纠结“模型是否真正理解”。做项目时，更重要的是知道它在哪些任务上可靠，在哪些任务上必须加边界。

## 在项目里怎么出现

常见用途：

- 对话和文本生成
- 代码生成和修改
- 信息抽取和结构化
- [[RAG]] 问答
- [[Agent]] 的规划、决策和工具调用

这些用途看起来都叫“调用模型”，但工程重点不同。抽取任务更关心格式稳定，RAG 更关心证据来源，Agent 更关心工具边界和多步失败恢复。

## 资料

- [OpenAI API docs](https://platform.openai.com/docs)：了解模型调用形态。
- [Hugging Face Transformers docs](https://huggingface.co/docs/transformers)：了解开源模型工程生态。
