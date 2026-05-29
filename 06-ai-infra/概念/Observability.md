# Observability

Observability 是让系统出问题时能查清楚发生了什么。AI 应用尤其需要它，因为一次失败可能来自用户输入、prompt、检索、模型、工具、网络、供应商限流或前端渲染。

“模型效果不好”不是可排查的问题。你需要把它拆成可观察的事件和指标。

## 三类信号

日志记录发生了什么。Metrics 记录数量和趋势。Trace 把一次请求经过的多个步骤串起来。

AI 系统至少要记录：

- request id / trace id
- user id 或 org id
- model、prompt version、模型参数
- input tokens、output tokens
- latency、TTFT、stream duration
- retrieved source ids
- tool calls
- error type
- eval result 或人工反馈

不是所有内容都能原样记录。用户隐私、secret、内部资料要脱敏或不落盘。

## 工程形态

一次 Agent 请求的 trace 可以像这样：

```text
HTTP request
  -> auth
  -> retrieve docs
  -> model call
  -> tool call
  -> model call
  -> SSE response
```

每个 span 都有耗时、输入输出摘要和错误。这样才能知道慢在检索、模型、工具还是网络。

## 常见失败模式

- 只有应用日志，没有模型、token 和工具字段。
- 日志记录完整 prompt，泄露用户数据。
- 只有平均延迟，没有 p95/p99。
- eval 结果和模型版本没有关联。
- 自部署只看 GPU 利用率，不看队列和 TTFT。

Observability 的目标不是做漂亮 dashboard，而是能回答：谁受影响、哪里坏了、为什么坏、改动后有没有恢复。

## 参考资料

- [OpenTelemetry docs](https://opentelemetry.io/docs/)
- [vLLM production metrics](https://docs.vllm.ai/en/latest/)
- [LangSmith evaluation docs](https://docs.smith.langchain.com/evaluation)
- [[Evals]]
- [[Cost]]
