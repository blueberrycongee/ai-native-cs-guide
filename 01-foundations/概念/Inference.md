# Inference

Inference 是模型被用来产生输出的过程。对大语言模型来说，最常见的形式是：给模型一段上下文，让它一个 [[Token]] 接一个 token 生成回答。

训练改变参数。推理使用已经训练好的参数。应用开发里，大多数成本、延迟、流式输出、限流和失败恢复问题都发生在推理链路上。

## 工作机制

一次生成通常分成两个阶段：

- prefill：处理输入上下文，建立 KV cache。
- decode：逐步生成新 token，每一步都用已有 KV cache。

首 token 延迟主要受 prefill、排队和路由影响。完整回答时间主要受输出长度、decode 速度和并发调度影响。

采样参数会改变输出形态。temperature、top_p、max_tokens、stop sequence 这类配置不应该被当成魔法按钮，它们只是控制候选 token 的选择方式和停止条件。

## 工程形态

一个后端推理链路常见结构是：

```text
client -> API server -> auth/rate limit -> prompt builder
       -> model API or self-hosted server
       -> stream parser -> SSE/WebSocket -> client
```

如果是自部署，还会多出模型权重加载、GPU 调度、batching、KV cache 管理、监控和降级策略。[[AI Infra]] 关心的就是这部分。

## 最小例子

应用层通常需要把模型调用包成可取消、可重试、可观测的操作：

```python
with trace("llm_call") as span:
    stream = client.responses.stream(
        model=model,
        input=messages,
        max_output_tokens=800,
    )
    for event in stream:
        if request_cancelled():
            stream.close()
            break
        yield to_sse(event)
```

这里的重点是系统行为：用户取消时要停上游请求；流式事件要可解析；错误要区分鉴权、限流、超时和模型拒答。

## 边界和失败模式

常见失败包括：

- 用户关闭页面后模型请求仍在运行。
- 所有错误都显示成“AI 失败”，无法定位 401、429、5xx 或解析错误。
- 流式输出没有事件边界，前端 Markdown、JSON 或工具状态渲染混乱。
- 自部署只看 tokens/s，不看排队时间、首 token 延迟和 p95 延迟。

做推理系统时，日志比口头判断可靠。至少记录模型、输入输出 token、延迟、错误类型、trace id 和是否命中缓存。

## 参考项目和资料

- [OpenAI Responses API docs](https://platform.openai.com/docs/api-reference/responses)：看托管模型推理接口。
- [vLLM quickstart](https://docs.vllm.ai/en/latest/getting_started/quickstart.html)：看 OpenAI-compatible 自部署推理服务。
- [SGLang documentation](https://docs.sglang.ai/)：看结构化生成和高性能推理。
- [[Streaming Response]]：把推理结果稳定返回给前端。
