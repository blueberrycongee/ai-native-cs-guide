# SSE

SSE，全称 Server-Sent Events，是服务器通过 HTTP 向浏览器单向推送事件的机制。浏览器原生提供 `EventSource`，后端只要按事件流格式持续写响应。

在 AI 应用里，SSE 最常见的用途是把模型输出逐步推给前端。用户不必等完整回答生成完，能更早看到首 token、工具状态或错误事件。

## 工作机制

SSE 响应的内容类型通常是 `text/event-stream`。服务端持续写入事件：

```text
event: delta
data: {"text":"hello"}

event: done
data: {}
```

每个事件用空行分隔。前端收到事件后逐步更新 UI。它适合“服务器持续告诉浏览器发生了什么”的场景，不适合高频双向通信。

## 工程形态

一个常见链路是：

```text
model stream -> backend parser -> SSE events -> browser renderer
```

后端不应该把上游模型事件原样暴露给前端。更稳的做法是定义自己的事件类型，比如 `message_delta`、`tool_started`、`tool_result`、`error`、`done`。这样以后换模型供应商时，前端不需要跟着重写。

## 最小例子

浏览器侧可以这样消费：

```javascript
const source = new EventSource("/api/chat/stream");

source.addEventListener("message_delta", (event) => {
  const payload = JSON.parse(event.data);
  appendText(payload.text);
});

source.addEventListener("done", () => {
  source.close();
});
```

如果需要发送用户输入，通常先用普通 HTTP POST 创建任务，再用 SSE 监听任务事件。若需要持续双向通信，再考虑 [[WebSocket]]。

## 边界和失败模式

最常见的问题是“代码用了 stream，但浏览器一次性收到结果”。排查顺序：

- 后端框架是否缓冲响应。
- Nginx、CDN 或平台网关是否开启缓冲。
- 响应头是否是 `text/event-stream`。
- 后端是否在收到上游 token 后及时 flush。
- 前端是否用了 `fetch().text()` 这类等完整 body 的写法。

还要处理断线重连、用户取消和错误事件。模型输出中途失败时，不要让前端停在半截 loading。

## 参考资料

- [MDN Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)：浏览器 API 和事件格式。
- [OpenAI streaming docs](https://platform.openai.com/docs/api-reference/responses/create)：看模型侧流式事件。
- [[Streaming Response]]：更通用的流式响应设计。
