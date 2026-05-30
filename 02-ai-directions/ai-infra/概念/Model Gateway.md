# Model Gateway

Model Gateway 是应用和模型后端之间的控制层。它统一鉴权、模型选择、日志、超时、重试、流式事件、成本记录和错误格式。

如果前端或业务服务直接到处调用模型 API，系统很快会失控：key 泄露、错误不可归因、模型升级难回滚、成本无法按用户拆分。

## 工作机制

最小结构是：

```text
client
  -> gateway auth / quota / trace
  -> prompt and request normalization
  -> provider or inference backend
  -> stream normalization
  -> logs / metrics / eval hooks
```

网关不应该把上游事件原样透传给前端。更稳的做法是定义自己的事件协议，例如 `message_delta`、`tool_call`、`error`、`done`。

## 最小实现

MVP 要做这些：

- 后端保存供应商 API key。
- 每个请求生成 trace id。
- 支持普通响应和 [[SSE]]。
- 记录模型、输入 token、输出 token、延迟、错误类型。
- 对 401、429、5xx、timeout 做不同处理。
- 用户取消后关闭上游请求。

这就是 [[项目路线]] 里的模型调用网关。

## 关键取舍

自己写网关能学到系统边界，但长期维护成本高。LiteLLM、SGLang Model Gateway 或云厂商网关能节省工作，但你仍要理解它们如何处理 fallback、budget、routing 和 observability。

不要把所有供应商参数暴露给业务方。业务方需要稳定抽象，不需要知道每个后端的全部细节。

## 常见失败模式

- API key 出现在浏览器或日志里。
- 用户取消页面后，上游模型还在生成。
- 所有错误都包装成 500。
- retry 没有上限，429 被立刻重试。
- streaming 事件没有统一协议，前端被供应商格式绑死。

## 参考资料

- [OpenAI Responses API](https://platform.openai.com/docs/api-reference/responses)
- [vLLM OpenAI-compatible server](https://docs.vllm.ai/en/latest/getting_started/quickstart.html)
- [SGLang Model Gateway](https://docs.sglang.io/advanced_features/sgl_model_gateway.html)
- [[API Auth]]
- [[Cost]]
