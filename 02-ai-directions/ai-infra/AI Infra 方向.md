# AI Infra 方向

AI Infra 不是“把模型部署起来”这么窄。它处理的是模型进入真实系统后的那一组硬问题：请求怎么进来，推理怎么排队，流式结果怎么返回，失败怎么定位，成本怎么归因，模型升级后质量有没有退化。

这条方向和 [[Agent]]、[[RAG]]、[[Post-Train]] 都会交叉。Agent 需要日志、工具事件和取消机制；RAG 需要检索服务、索引版本和权限隔离；后训练模型最终也要进入推理服务和评测流水线。

## 方向边界

AI Infra 当前先收敛在五个问题上：

- [[Model Gateway]]：统一模型 API、鉴权、日志、超时、重试和流式事件。
- [[Inference Serving]]：自部署模型时如何处理 batch、KV cache、吞吐和延迟。
- [[Observability]]：怎样用日志、metrics、trace 和 eval 定位问题。
- [[Model Routing]]：如何在多个模型、供应商或推理后端之间选择。
- [[GPU Basics]]：为什么显存、带宽、并发和利用率会限制 AI 服务。

训练平台、特征平台、数据湖和全量 MLOps 也属于更宽的 AI Infra。这个目录先收敛在模型调用、推理服务、观测、路由和 GPU 资源这些直接影响 AI 应用运行的问题上。

## 工程边界

AI Infra 经常被误解成“把平台搭起来”。真正影响系统质量的通常是更具体的环节：请求是否能取消，流式事件是否稳定穿过代理，错误能否分类，模型版本和 prompt 版本能否关联到 eval 结果，自部署服务能否解释 queue time、TTFT、tokens/s 和显存占用。

OpenAI-compatible 也不是完全兼容。不同推理引擎对 streaming、tool call、structured output、错误码和采样参数的支持并不总一致。模型网关的价值就在于隔离这些差异。

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
