# HTTP

HTTP 是 AI 应用最常见的系统边界。前端请求后端、后端调用模型 API、上传文件、触发 [[RAG]] 检索、返回 [[Streaming Response]]，大多都从 HTTP 开始。

这里不把 HTTP 写成面试题。对 AI 应用来说，先理解请求、响应、状态码、header、body、超时、重试和取消，比背完整 RFC 更有用。

## 在 AI 链路里的位置

一次模型请求通常不是“前端直接调模型”这么简单：

```text
browser -> your backend -> model provider or inference server
        <- stream/events/errors <-
```

后端要处理 [[API Auth]]、限流、日志、上下文拼接、用户取消和错误归类。只把 API key 放在前端会泄露凭证，也无法做统一审计。

## 关键机制

AI 请求经常是慢请求。它可能几十秒才返回，也可能一边返回一边生成。HTTP 层要关心：

- timeout：请求最多等多久。
- retry：失败后是否重试，重试几次。
- idempotency：重试会不会重复创建任务或重复扣费。
- status code：401、403、408、429、5xx 是不同问题。
- headers：鉴权、缓存、内容类型、SSE 控制都在这里。
- cancellation：用户取消后，后端要停止上游调用。

这些不是理论细节。少一个取消处理，线上就可能继续烧 token。

## 最小例子

一个后端调用模型时至少要区分错误：

```python
try:
    response = llm_client.generate(request, timeout=30)
except AuthError:
    return 401, {"error": "invalid_api_key"}
except RateLimitError:
    return 429, {"error": "rate_limited"}
except TimeoutError:
    return 504, {"error": "upstream_timeout"}
```

真实项目还要加 trace id、用户 id、模型名、token 用量和耗时。没有这些字段，后面做 [[Evals]]、成本分析和问题排查都会很痛苦。

## 边界和失败模式

常见问题：

- 前端一直 loading，后端其实已经超时。
- 所有上游错误都被包装成 500。
- 代理缓冲响应，[[SSE]] 变成一次性返回。
- 上传大文件没有大小限制，后端内存被打满。
- 重试策略没有区分 POST 副作用，导致重复任务。

HTTP 不是 AI 项目的亮点，但它决定系统能不能稳定工作。

## 参考资料

- [MDN HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)：基础概念。
- [MDN HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)：遇到状态码时查。
- [RFC 9110 HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)：需要精确定义时再看。
