# Context Engineering

Context Engineering 是为模型组织上下文的工程方法。它关心哪些信息进入 [[Context Window]]、顺序如何安排、如何压缩、如何更新，以及如何避免无关信息污染任务。

它比 [[Prompting]] 宽。Prompting 写任务说明；context engineering 还包括检索资料、历史消息、工具结果、运行状态、记忆和成本预算。

## 工作机制

模型不会自动知道你的系统状态。你必须把当前任务需要的信息放进上下文，或者让模型通过工具去取。

一个可控的上下文通常会拆成几类：

```text
system rules
user request
short-term conversation state
retrieved evidence
tool outputs
durable task state
output contract
```

每一类都应该有 token 预算和进入条件。否则上下文会越堆越长，最后既贵又不准。

## 工程形态

真实项目里，context engineering 通常落在这些组件上：

- prompt builder：把不同来源拼成模型输入。
- retriever：为 [[RAG]] 找证据。
- summarizer：压缩历史对话或长工具输出。
- memory / state store：不同系统里可能是 checkpoint、workspace 文件、向量索引或外部 memory provider。
- policy layer：决定哪些内容不能进 prompt。
- eval set：比较不同上下文策略的效果。

Agent 系统尤其需要这层。工具结果、文件修改、待办状态和用户确认，不能只靠对话历史保存。

## 最小例子

一个 RAG 问答的上下文构造可以写成明确规则：

```python
context = [
    system_rules,
    format_contract,
    recent_messages.limit_tokens(1200),
    retrieved_chunks.top_k(6).with_citations(),
    user_question,
]
```

这段代码背后的判断比 prompt 文案更重要：最近消息只能占 1200 tokens；检索片段必须带来源；用户问题放在最后，减少被长资料淹没的概率。

## 边界和失败模式

常见失败包括：

- 把所有历史消息都带上，关键信息反而丢掉。
- 工具输出太长，没有摘要和结构化。
- RAG 检索结果没有来源和权限过滤。
- 长任务状态只存在对话里，重试或断线后无法恢复。
- 只优化单次回答，不记录上下文策略对成本和延迟的影响。

Context engineering 的完成标准不是“prompt 很完整”，而是系统能解释每段上下文为什么出现、从哪里来、什么时候删除。

## 参考资料

- [Anthropic Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval)：看给 chunk 补上下文的一种检索策略。
- [OpenAI prompt caching](https://platform.openai.com/docs/guides/prompt-caching)：理解稳定前缀如何影响成本和延迟。
- [[Memory]]：Hermes Agent、OpenClaw、Mem0、LangGraph 等具体 memory 实现入口。
