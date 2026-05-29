# Streaming Response

Streaming response 是边生成边返回。AI 应用需要它，是因为 [[Inference]] 往往不是瞬间完成：模型逐 token 生成，工具调用也可能分步骤返回。

没有流式响应，用户只能看到一个长时间 loading。更糟的是，系统也更难暴露中间状态、取消任务和定位卡住的位置。

## 工作机制

流式响应不是一种单独协议，而是一类返回方式。常见选择包括：

- HTTP chunked response：服务端持续写 body。
- [[SSE]]：在 HTTP 上封装事件，浏览器支持好。
- [[WebSocket]]：双向长连接，适合复杂实时交互。

对聊天应用来说，SSE 往往够用。对 Agent 控制台或语音交互，WebSocket 可能更合适。

## 工程形态

一个稳定的流式链路通常要有事件模型：

```text
run_started
message_delta
tool_call_started
tool_call_finished
error
done
```

不要只传文本。真实 AI 系统里，模型可能先解释计划，再调用工具，再返回工具结果，最后生成答案。前端需要知道每个事件的类型，而不是从一串文本里猜。

## 最小例子

后端可以把上游模型事件转换成自己的事件：

```python
for event in model_stream:
    if event.type == "text_delta":
        yield sse("message_delta", {"text": event.text})
    elif event.type == "tool_call":
        yield sse("tool_call_started", {"name": event.name})

yield sse("done", {})
```

这层转换很重要。它隔离模型供应商的事件格式，也让前端协议更稳定。

## 边界和失败模式

常见失败包括：

- 反向代理缓冲，流式变成一次性返回。
- 前端把 delta 当完整消息，导致重复渲染或丢字。
- 模型输出 Markdown 时，代码块没闭合，UI 抖动。
- JSON mode 流式输出半截 JSON，前端过早解析。
- 错误事件没有统一格式，用户只看到连接断开。
- 用户取消后没有关闭上游模型请求。

流式响应的完成标准不是“能看到字一个个出来”，而是错误、取消、重连、工具状态和最终结果都能被稳定处理。

## 参考资料

- [MDN Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API)：浏览器流处理基础。
- [MDN Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)：SSE 事件流。
- [[HTTP]]：理解状态码、header、超时和代理行为。
