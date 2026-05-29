# Context Window

Context window 是模型一次推理能看到的 token 范围。它包括 system prompt、用户输入、历史消息、工具结果、检索资料，以及模型正在生成的输出。

它不是“记忆”。请求结束后，模型不会自动保留这段上下文。下一次请求能看到什么，取决于应用把什么重新发给模型。

## 工作机制

对自回归模型来说，生成下一个 [[Token]] 时只能基于当前上下文。上下文越长，prefill 阶段需要处理的 token 越多，KV cache 占用也越大。

```text
system + history + retrieved docs + user request + partial answer
  -> model
  -> next token
```

窗口大小是硬限制，但质量限制更早出现。即使模型支持很长上下文，里面的噪声、冲突和位置效应也会影响输出。

## 工程形态

AI 应用通常需要自己管理上下文：

- 聊天系统要决定保留哪些历史消息。
- [[RAG]] 要决定放哪些检索片段、按什么顺序放。
- [[Agent]] 要决定工具调用结果是否写回上下文，写多少。
- 成本控制要估算输入和输出 token。
- 长任务要把中间状态存到数据库或文件系统，而不是全塞进 prompt。

上下文管理做不好，系统会出现两种相反的问题：要么模型缺信息，要么信息太多导致延迟、成本和错误一起上升。

## 最小例子

一个朴素的上下文预算可以这样切：

```text
system prompt:       800 tokens
recent messages:   2500 tokens
retrieved context: 4000 tokens
tool results:      1500 tokens
output budget:     1200 tokens
```

这不是通用配方。不同任务要调整比例。代码修复可能需要更多文件上下文；客服问答可能需要更严格的来源引用；Agent 运行日志不应该全部进入模型。

## 边界和失败模式

常见失败包括：

- 把 context window 当长期记忆，结果下一轮丢状态。
- 只追求更长窗口，不做检索、摘要和状态建模。
- 工具输出原样塞回 prompt，日志和 JSON 把有用信息挤掉。
- 忽略输出预算，回答被截断。
- RAG 召回内容没有来源、时间和权限元数据，模型难以判断可信度。

真正可靠的上下文工程通常会把短期对话、长期记忆、外部知识、工具状态和审计日志分开存储。[[Context Engineering]] 讲的是这套系统设计，而不只是 prompt 写法。

## 参考资料

- [Anthropic prompt engineering: long context tips](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/long-context-tips)：看长上下文使用建议。
- [OpenAI text generation docs](https://platform.openai.com/docs/guides/text)：看模型输入输出和上下文的基本接口。
- [[Token]]：上下文窗口按 token 计算。
- [[RAG]]：把外部资料按需放入上下文。
