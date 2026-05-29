# AI Infra

AI Infra 不是“把模型部署起来”这么窄。它处理的是模型进入真实系统后的那一组硬问题：请求怎么进来，推理怎么排队，流式结果怎么返回，失败怎么定位，成本怎么归因，模型升级后质量有没有退化。

这条方向和 [[Agent]]、[[RAG]]、[[Post-Train]] 都会交叉。Agent 需要日志、工具事件和取消机制；RAG 需要检索服务、索引版本和权限隔离；后训练模型最终也要进入推理服务和评测流水线。

## 方向边界

AI Infra 当前先收敛在五个问题上：

- [[Model Gateway]]：统一模型 API、鉴权、日志、超时、重试和流式事件。
- [[Inference Serving]]：自部署模型时如何处理 batch、KV cache、吞吐和延迟。
- [[Observability]]：怎样用日志、metrics、trace 和 eval 定位问题。
- [[Model Routing]]：如何在多个模型、供应商或推理后端之间选择。
- [[GPU Basics]]：为什么显存、带宽、并发和利用率会限制 AI 服务。

训练平台、特征平台、数据湖和全量 MLOps 也属于更宽的 AI Infra，但本仓库先不把范围拉太大。读者需要先做出能稳定服务小流量的 AI 应用，再进入大平台。

## 推荐进入顺序

1. 读 [[AI Infra 路径]]，明确第一个项目是模型调用网关。
2. 补 [[HTTP]]、[[SSE]]、[[Streaming Response]]、[[API Auth]] 和 [[Inference]]。
3. 做一个网关，把托管模型 API 包起来，记录 token、延迟和错误。
4. 再接一个 vLLM 或 SGLang 后端，理解自部署推理和托管 API 的差异。
5. 用 [[Evals]] 和日志比较模型版本，不靠感觉判断“效果变好”。

## 需要避免的误区

不要一开始就堆 Kubernetes、队列、服务网格和复杂监控。没有真实请求、错误和成本数据时，平台化很容易变成空架子。

也不要把 OpenAI-compatible 当成完全兼容。不同推理引擎对 streaming、tool call、structured output、错误码和采样参数的支持并不总一致。模型网关要隔离这些差异。

## 目录入口

- [[AI Infra 路径]]
- [[AI Infra 信息来源]]
- [[AI Infra 开源项目]]
- [[AI Infra 框架和工具]]
- [[Inference Serving]]
- [[Model Gateway]]
- [[Observability]]
- [[Model Routing]]
- [[GPU Basics]]
