# HTTP

HTTP 是 AI 应用最常见的网络入口。你调用模型 API、上传文件、发起 RAG 查询、让前端请求后端，通常都绕不开它。

这里不把 HTTP 写成面试题。对 AI 应用开发来说，先理解请求、响应、状态码、超时、重试和流式返回就够用。

相关概念：

- [[API Auth]]
- [[Streaming Response]]
- [[SSE]]
- [[WebSocket]]
- [[Inference]]

## 为什么要学

模型调用经常是慢请求。它可能几十秒才返回，也可能中途失败，还可能因为限流、网络或上游模型问题返回错误。

如果不理解 HTTP，常见问题会很难排查：

- 前端一直 loading，但后端其实超时了
- 用户取消了请求，模型调用还在继续烧钱
- 429、401、500 被统一当成“模型坏了”
- 流式输出被代理或框架缓冲，前端迟迟看不到首 token

## 学到什么程度

先掌握：

- method、header、body 的基本作用
- 2xx、4xx、5xx 状态码的大致含义
- timeout、retry、idempotency 的区别
- 长请求为什么需要取消和错误处理
- 代理、网关、浏览器和后端框架可能改变响应行为

不用一开始背完整 RFC。等你遇到缓存、跨域、连接复用或代理问题，再查官方文档。

## 在 AI 项目里怎么出现

常见场景：

- 后端通过 HTTP 调用 LLM API
- 前端通过 HTTP 请求后端发起 [[Inference]]
- 后端用 [[SSE]] 返回 [[Streaming Response]]
- 上传文件后做 embedding、检索和总结
- API key 通过 [[API Auth]] 管理

## 资料

- [MDN HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)：适合查 HTTP 基础。
- [MDN HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)：遇到状态码时查，不用背。
