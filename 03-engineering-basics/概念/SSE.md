# SSE

SSE，全称 Server-Sent Events，是服务器通过 HTTP 向浏览器单向推送事件的机制。浏览器原生提供 `EventSource`，后端只要按事件流格式持续写响应。

在 AI 应用里，SSE 最常见的用途是把模型输出逐步推给前端。用户不必等完整回答生成完，能更早看到首 token、工具状态或错误事件。

## 通信方向

SSE 是单向通信：服务器向客户端推送事件，客户端不能通过同一条 SSE 连接向服务器发送消息。

这点和 [[WebSocket]] 不一样。WebSocket 是双向连接，客户端和服务端都可以随时发送消息。SSE 更适合服务端持续通知浏览器的场景，例如模型 token、任务进度、工具状态、通知和日志。

如果用户输入也要发送到后端，通常用普通 HTTP 请求完成：

```text
POST /api/chat        -> 创建任务或发送用户输入
GET  /api/chat/events -> 用 SSE 接收服务端事件
```

如果一个功能需要高频双向通信，例如协同编辑、多人游戏、语音交互或复杂控制台，再考虑 WebSocket。

## 网络协议

SSE 不是独立的传输层协议。它运行在 HTTP 上，本质是一次长时间保持打开的 HTTP 响应，服务端持续向 response body 写入事件流。

关键响应头通常包括：

```http
Content-Type: text/event-stream
Cache-Control: no-cache
Connection: keep-alive
```

HTTP/1.1 下通常依赖长连接和分块传输；HTTP/2 下可以作为一个长时间打开的 stream。对应用开发者来说，重点是：SSE 走普通 HTTP 基础设施，但反向代理、CDN、网关和后端框架可能会缓冲响应，导致前端不是逐步收到事件，而是最后一次性收到。

## 工作机制

SSE 响应的内容类型通常是 `text/event-stream`。服务端持续写入事件：

```text
event: delta
data: {"text":"hello"}

event: done
data: {}
```

每个事件用空行分隔。前端收到事件后逐步更新 UI。它适合“服务器持续告诉浏览器发生了什么”的场景，不适合高频双向通信。

## 数据格式

SSE 的 wire format 是 UTF-8 文本事件流，MIME type 是 `text/event-stream`。它不是 JSON 协议，但 `data:` 字段里经常放 JSON 字符串。

常用字段有：

- `event`：事件类型。省略时浏览器触发默认 `message` 事件。
- `data`：事件数据。可以出现多行，浏览器会把多行 data 合并。
- `id`：事件 ID。浏览器重连时会用它记录最后收到的事件。
- `retry`：建议浏览器断线后等待多少毫秒再重连。
- 以 `:` 开头的行：注释，常用作 heartbeat，防止连接被中间层关闭。

一个完整事件例子：

```text
id: 42
event: message_delta
data: {"text":"hello"}

```

注意最后的空行。没有空行，浏览器不会认为这个事件已经结束。

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

## 自动重连

浏览器的 `EventSource` 支持自动重连。连接异常断开时，浏览器会等待一段时间后重新请求同一个 URL。

服务端可以用 `retry` 字段调整重连间隔：

```text
retry: 3000

```

如果服务端发送过 `id` 字段，浏览器会记录最后的 event id。重连时，浏览器会通过 `Last-Event-ID` 请求头把这个 ID 发给服务端。服务端可以据此补发遗漏事件，或者从某个任务状态继续推送。

自动重连不是可靠消息队列。要想做到断线恢复，服务端必须保存事件或任务状态，并定义从 `Last-Event-ID` 恢复的逻辑。否则浏览器虽然会重连，但中间丢失的事件不一定能补回来。

如果任务已经结束，前端应该主动调用 `source.close()`，否则浏览器可能继续重连。

## 浏览器兼容性

现代主流浏览器基本都支持 `EventSource`，包括 Chrome、Edge、Firefox、Safari，以及对应的移动端浏览器。MDN 将 `EventSource` 标为 widely available。

需要注意几个实际限制：

- 老旧浏览器和某些非浏览器运行环境可能没有原生 `EventSource`。
- `EventSource` 默认只能发 GET 请求；如果需要 POST、复杂请求头或自定义鉴权头，通常要改用 fetch streaming、自建协议，或把鉴权放在 cookie / URL token / session 里。
- 跨域 SSE 要按 CORS 处理；如果要带 cookie，需要创建 `new EventSource(url, { withCredentials: true })`，服务端也要返回正确的 CORS header。
- HTTP/1.1 下浏览器对同一 origin 的并发连接数有限。多个页面或多个 SSE 连接可能互相占用连接名额。

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
- [MDN EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource)：`EventSource` API、单向通信和兼容性。
- [WHATWG HTML Server-sent events](https://html.spec.whatwg.org/dev/server-sent-events.html)：SSE 标准里的事件流格式、`Last-Event-ID` 和重连语义。
- [OpenAI streaming docs](https://platform.openai.com/docs/api-reference/responses/create)：看模型侧流式事件。
- [[Streaming Response]]：更通用的流式响应设计。
