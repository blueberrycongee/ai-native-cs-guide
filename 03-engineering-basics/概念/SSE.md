# SSE

SSE，全称 Server-Sent Events，是服务器向浏览器单向推送事件的机制。它基于 HTTP，浏览器原生支持 `EventSource`。

在 AI 应用里，SSE 最常见的用途是把模型输出流式推给前端。

相关概念：

- [[HTTP]]
- [[Streaming Response]]
- [[Inference]]
- [[WebSocket]]

## 为什么常用于模型输出

模型通常是一个 token 接一个 token 生成。等完整回答生成完再返回，用户会觉得慢。

SSE 可以让前端更早看到内容：

- 后端收到模型流式输出
- 后端把 token 或事件逐步转成 SSE
- 前端逐步渲染文本、状态或工具结果

这个链路比 WebSocket 简单，因为大多数场景只需要服务器向浏览器推送。

## 学到什么程度

先掌握：

- SSE 是单向推送，不适合复杂双向通信
- 响应头和代理缓冲会影响是否真的流式返回
- 前端需要处理断线、重连和错误
- 后端要在用户取消时停止上游模型请求

暂时不必背事件格式的所有细节。会查 MDN，能写出稳定链路，就够开始做项目。

## 容易踩坑

最常见的问题是“代码看起来用了 stream，浏览器却一次性收到结果”。

通常要检查：

- 框架有没有缓冲响应
- 反向代理有没有缓冲
- 响应头是否适合 SSE
- 后端是否真的边收到模型输出边 flush
- 前端是不是等完整 body 才渲染

## 资料

- [MDN Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)：浏览器侧基础。
- [OpenAI Streaming API docs](https://platform.openai.com/docs/api-reference/responses/create)：看模型流式输出如何进入后端。
