# WebSocket

WebSocket 是浏览器和服务器之间的双向长连接。它适合双方都要持续发消息的场景：协作编辑、实时状态、语音流、复杂 Agent 控制台。

AI 应用里不要一看到“实时”就上 WebSocket。模型文本流式输出通常用 [[SSE]] 更简单。WebSocket 的价值在于双向、低延迟、长会话和更细粒度的控制。

## 工作机制

WebSocket 先通过 HTTP 完成握手，然后升级成持久连接。连接建立后，浏览器和服务器都可以主动发送消息。

```text
browser <==== persistent connection ====> backend
```

这和普通 HTTP 请求不同。你需要自己处理连接生命周期、心跳、重连、鉴权、消息协议和背压。

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
- [RFC 6455](https://www.rfc-editor.org/rfc/rfc6455)：协议定义，需要精确行为时查。
- [[SSE]]：单向模型输出的更简单选择。
