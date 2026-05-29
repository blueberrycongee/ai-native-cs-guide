# LLM

LLM，也就是大语言模型，是基于上下文生成文本、代码或结构化输出的模型。大多数现代 LLM 基于 [[Transformer]]，输入输出都按 [[Token]] 处理。

在这个仓库里，LLM 不是孤立概念。它是 [[Prompting]]、[[RAG]]、[[Agent]]、[[Context Engineering]] 和 [[Inference]] 的共同底座。

## 工作机制

应用层最该记住的一点是：LLM 不是数据库查询，也不是规则引擎。它根据当前上下文预测下一个 token，再把生成结果接回上下文继续预测。

```text
messages + retrieved context + tool results
  -> model
  -> next token distribution
  -> generated output
```

采样参数、系统提示、上下文顺序、工具结果和模型版本都会影响输出。这个特性让 LLM 很灵活，也让它不像普通函数那样稳定。

## 工程形态

常见用法包括：

- 对话和写作。
- 代码生成、解释和修改。
- 信息抽取和结构化输出。
- 基于证据的 [[RAG]] 问答。
- [[Agent]] 中的规划、路由和工具参数生成。

这些看起来都叫“调用模型”，但系统重点不同。抽取任务关心格式稳定；RAG 关心证据来源；Agent 关心工具权限、循环控制和失败恢复。

## 最小例子

一个面向工程的模型调用不应该只返回文本：

```json
{
  "model": "example-model",
  "input_tokens": 1820,
  "output_tokens": 410,
  "latency_ms": 3860,
  "finish_reason": "stop",
  "trace_id": "run_123"
}
```

这些元数据决定你能不能排查成本、延迟和退化。没有日志的 LLM 应用，很快会变成凭感觉调 prompt。

## 边界和失败模式

LLM 常见失败包括：

- 编造没有证据的事实。
- 输出格式看似 JSON，实际无法解析。
- 在长上下文中忽略关键约束。
- 受 prompt injection 影响，尝试越权调用工具。
- 模型升级后旧 prompt 退化。

工程上不要指望一个更长的 system prompt 解决所有问题。需要检索、权限、结构化输出校验、[[Evals]] 和日志一起工作。

## 参考资料

- [OpenAI API docs](https://platform.openai.com/docs)：了解托管模型调用形态。
- [Hugging Face Transformers](https://huggingface.co/docs/transformers)：了解开源模型加载、生成和训练生态。
- [[Inference]]：模型如何在系统里产生输出。
