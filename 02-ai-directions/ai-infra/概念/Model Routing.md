# Model Routing

Model Routing 是在多个模型、供应商或推理后端之间选择请求去向。它可以为了成本、延迟、质量、可用性、上下文长度、数据合规或工具能力。

路由不是简单的“失败就换模型”。不同模型输出风格、工具调用格式、上下文限制和安全策略可能不同。切换后要有 eval 和回滚。

## 常见路由策略

- 按任务路由：分类、改写、抽取用小模型，复杂推理用强模型。
- 按成本路由：免费用户或低风险任务优先便宜模型。
- 按延迟路由：低延迟任务走本地或轻量模型。
- 按能力路由：需要长上下文、工具调用或结构化输出时选择支持更好的后端。
- 按可用性路由：供应商限流或故障时 fallback。

## 工程形态

路由层通常放在 [[Model Gateway]] 里：

```text
request metadata
  -> policy
  -> model candidate list
  -> provider call
  -> normalized response
  -> logs and eval
```

策略输入可以包括用户等级、任务类型、prompt 版本、上下文长度、历史成功率和当前后端健康状态。

## 常见失败模式

- fallback 到弱模型后没有告诉评测系统，质量退化难定位。
- 不同后端 tool call 格式不同，业务代码解析失败。
- 路由策略只看价格，不看失败率和重试成本。
- A/B 测试没有固定样例和用户隔离，路由策略变了好坏说不清。

模型切换后的关键状态是可观测、可回滚、可解释，而不只是“能切模型”。

## 参考项目

- [LiteLLM](https://github.com/BerriAI/litellm)：多供应商代理、budget、fallback 的常见选择。
- [SGLang Model Gateway](https://docs.sglang.io/advanced_features/sgl_model_gateway.html)：看模型路由、观测和请求传播。
- [Ray Serve LLM](https://docs.ray.io/en/latest/serve/llm/index.html)：看多模型服务和路由能力。
- [[Evals]]
