# Prompt Cache

Prompt Cache 是复用重复上下文来降低延迟和成本的机制。它常出现在长 system prompt、长文档、多轮任务和 Agent 工作流里。

它优化的是重复计算，不保证答案质量变好。上下文本身如果是错的，缓存只会让错误更便宜。

## 工作机制

很多请求有稳定前缀：

```text
system rules + tool descriptions + repository summary + user request
```

如果前缀相同或满足供应商的缓存规则，推理系统可以复用这部分上下文的计算结果。对自部署模型来说，相近问题会落到 KV cache、prefix cache 或调度策略；对托管 API 来说，缓存规则由供应商定义。

## 工程形态

要让 prompt cache 有用，上下文要按“稳定前缀 + 变化输入”组织：

- 系统规则、工具说明、长文档摘要放前面，尽量稳定。
- 用户本轮输入、临时检索结果和动态状态放后面。
- 不要在前缀里插入时间戳、随机 id 或每轮变化的日志。
- 记录缓存命中率、输入 token、首 token 延迟和费用。

Agent 和代码助手特别容易受益，因为它们每轮都会携带类似的仓库规则、工具说明和安全约束。

## 最小例子

```text
[stable prefix]
  system rules
  tool schemas
  repository conventions

[dynamic suffix]
  current user request
  selected file snippets
  latest tool output
```

这不是提示词美化，而是成本结构设计。把高频变化内容放到前缀里，会让缓存失效。

## 边界和失败模式

常见失败包括：

- 期待缓存改善答案质量。
- 每次请求都重排工具说明，导致前缀不稳定。
- 把用户隐私数据放进可复用上下文，没有隔离策略。
- 不记录命中率，无法判断优化是否有效。
- 因为追求缓存，把本该更新的上下文固定住。

使用托管模型时，要以供应商文档为准。不同 API 的缓存粒度、计费和失效规则可能差很多。

## 参考资料

- [OpenAI prompt caching](https://platform.openai.com/docs/guides/prompt-caching)：看自动缓存规则。
- [Anthropic prompt caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)：看显式 cache breakpoint。
- [[Cost]]：缓存如何进入成本模型。
