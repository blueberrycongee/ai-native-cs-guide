# WebSocket

WebSocket 是浏览器和服务器之间的双向长连接。它适合双方都要持续发消息的场景：协作编辑、实时状态、语音流、复杂 Agent 控制台。

AI 应用里不要一看到“实时”就上 WebSocket。模型文本流式输出通常用 [[SSE]] 更简单。WebSocket 的价值在于双向、低延迟、长会话和更细粒度的控制。

## 通信方向

WebSocket 是全双工通信。连接建立后，客户端和服务器都可以在任意时刻主动发送消息，不需要等对方先发请求。

这点和 [[SSE]] 的边界正好相反。SSE 是服务端到客户端的单向事件流；WebSocket 是双方共享一条持久连接，并在这条连接上交换应用消息。

适合 WebSocket 的前提通常是：客户端也需要持续向服务端发控制信号或实时数据，而不只是接收模型输出。

## 网络协议

WebSocket 是独立的应用层协议，标准定义在 RFC 6455。它通常先通过 HTTP/1.1 发起握手，请求里带 `Upgrade: websocket`，服务端返回 `101 Switching Protocols` 后，连接从 HTTP 请求/响应语义切换成 WebSocket 帧通信。

URL scheme 通常是：

```text
ws://example.com/socket
wss://example.com/socket
```

`wss` 是跑在 TLS 上的 WebSocket，生产环境通常应该使用 `wss`。如果网页本身是 HTTPS，浏览器通常也会要求使用安全的 WebSocket 连接，否则会遇到 mixed content 或安全策略问题。

握手之后，通信不再是普通 HTTP response body。中间层必须支持 WebSocket upgrade；反向代理、负载均衡和网关如果没配置好，连接会在握手阶段失败或空闲时被断开。

## 工作机制

WebSocket 先通过 HTTP 完成握手，然后升级成持久连接。连接建立后，浏览器和服务器都可以主动发送消息。

```text
browser <==== persistent connection ====> backend
```

这和普通 HTTP 请求不同。你需要自己处理连接生命周期、心跳、重连、鉴权、消息协议和背压。

## 数据格式

WebSocket 协议层传的是 frame。应用层通常看到的是 message。

常见 frame/message 类型包括：

- text：UTF-8 文本。Web 应用里经常放 JSON。
- binary：二进制数据，例如音频片段、图片片段或自定义编码。
- close：关闭连接。
- ping / pong：协议层心跳，用来检测连接是否还活着。

WebSocket 本身不规定你的业务消息格式。你需要自己定义应用协议，例如：

```json
{"type":"user_message","run_id":"r1","text":"继续"}
{"type":"agent_step","run_id":"r1","status":"running"}
{"type":"error","run_id":"r1","code":"permission_denied"}
```

这和 [[SSE]] 不同。SSE 有固定的 `event`、`data`、`id`、`retry` 文本字段；WebSocket 只给你双向消息通道，事件类型、请求响应关系、错误格式、版本兼容都要自己设计。

## 工程形态

适合 WebSocket 的 AI 场景：

- 语音助手：客户端上传音频片段，服务端返回识别、模型输出和语音合成状态。
- Agent 工作台：前端发送暂停、继续、批准工具调用等指令，服务端推送步骤日志。
- 多人协作：多个用户同时看同一个 AI 任务状态。
- 需要低延迟双向信令的工具调用。

不适合的场景也很常见：一个普通聊天框，只要把模型回答流式显示出来，用 SSE 或 streaming fetch 就够了。

## 最小协议

不要直接在 WebSocket 里随便发字符串。至少定义事件类型：

```json
{"type":"user_message","text":"帮我总结这份文档"}
{"type":"tool_approval","run_id":"r1","approved":true}
{"type":"agent_step","run_id":"r1","status":"running","title":"search docs"}
{"type":"error","code":"rate_limited"}
```

协议一旦被前端和后端共同依赖，就要保持向后兼容。否则调试会变成猜消息格式。

## 自动重连

浏览器原生 `WebSocket` 不提供自动重连。连接断开后，前端必须自己在 `close` 或 `error` 事件里决定是否重连、等多久重连、是否带上会话 ID，以及如何恢复状态。

一个常见策略是：

```text
onclose
  -> wait with backoff
  -> reconnect with session_id / run_id
  -> fetch missed state or ask server to replay events
```

自动重连不能只是在前端重新 `new WebSocket()`。如果后端任务仍在跑，服务端还要能把新连接重新绑定到原来的任务；如果消息需要可靠送达，还要有消息 ID、ack、事件缓存或状态快照。

所以 WebSocket 的断线恢复通常比 [[SSE]] 更需要应用层协议设计。SSE 的 `EventSource` 至少内置自动重连和 `Last-Event-ID` 语义；WebSocket 需要自己做。

## 浏览器兼容性

现代主流浏览器基本都支持 WebSocket，MDN 将 `WebSocket` 标为 widely available。它也可在 Web Worker 中使用。

实际限制主要不在浏览器是否支持，而在部署和协议设计：

- 生产页面通常要用 `wss`。
- 代理、网关和负载均衡必须支持 WebSocket upgrade。
- 原生 WebSocket API 不能直接设置任意自定义请求头；鉴权通常用 cookie、URL token、子协议或握手前的 HTTP 流程。
- 浏览器 API 本身没有自动重连。
- MDN 提醒原生 `WebSocket` API 没有内置 backpressure；消息到达速度超过应用处理速度时，可能造成内存增长或 UI 卡顿。

## 和 SSE 对照

两者不要按“谁更高级”选择，而要按通信模型选择。

| 维度 | SSE | WebSocket |
| --- | --- | --- |
| 通信方向 | 服务端到客户端单向 | 客户端和服务端双向 |
| 协议形态 | HTTP 长响应，`text/event-stream` | HTTP 握手后升级为 WebSocket 协议 |
| 浏览器 API | `EventSource` | `WebSocket` |
| 数据格式 | 文本事件流，`event/data/id/retry` | text/binary message，业务格式自定义 |
| 自动重连 | `EventSource` 内置重连 | 需要应用自己实现 |
| 典型 AI 场景 | 模型文本流、任务进度、服务端事件 | 语音流、Agent 控制台、双向实时协作 |

普通聊天输出优先用 [[SSE]]。需要客户端持续发送实时控制或音频数据，再用 WebSocket。

## 边界和失败模式

常见失败包括：

- 连接断了但后端任务继续跑，前端无法恢复状态。
- 鉴权只在握手时做，长连接期间权限变化没有处理。
- 没有心跳，代理悄悄断开连接。
- 消息没有 `run_id`，多个任务事件混在一起。
- 用 WebSocket 传大文件或大日志，阻塞其他事件。

WebSocket 更像实时系统组件，不是 HTTP 的简单替代品。用它之前先确认你真的需要双向通信。

## 参考资料

- [MDN WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)：浏览器侧基础。
- [MDN WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)：`WebSocket` 对象、兼容性和 backpressure 限制。
- [RFC 6455](https://www.rfc-editor.org/rfc/rfc6455)：协议定义，需要精确行为时查。
- [[SSE]]：单向模型输出的更简单选择。
