# GPU Basics

GPU Basics 在这里不讲完整 CUDA 教程，只讲 AI 服务里最容易影响判断的几个概念：显存、带宽、并行、利用率和队列。

很多人看到 GPU 利用率不高就以为还有空间，但 LLM 服务常被显存、KV cache、内存带宽或调度限制住，不一定是算力不够。

## 关键资源

- 显存：模型权重、KV cache、activation、batch 都要占用。
- 内存带宽：decode 阶段常受权重读取和 KV cache 访问影响。
- 计算吞吐：prefill 更容易吃满计算。
- PCIe/NVLink：多 GPU 或 CPU offload 时影响数据传输。
- 队列：请求太多时，用户感受到的是排队延迟。

## 和推理服务的关系

长上下文和高并发会放大 KV cache。模型越大，权重越占显存。batch 越大，吞吐可能提高，但单个请求延迟可能变差。

这就是 [[Inference Serving]] 要做调度的原因。服务不是把所有请求一起塞进 GPU，而是在吞吐、延迟、公平性和显存之间找平衡。

## 最小观察

跑推理服务时至少看：

- GPU memory used
- GPU utilization
- TTFT
- tokens/s
- request queue length
- p95 latency
- OOM 次数

只看 `nvidia-smi` 不够。它能告诉你资源状态，但不能告诉你用户请求慢在哪一步。

## 常见失败模式

- 只按模型权重大小估算显存，忽略 KV cache 和 activation。
- 用单请求 benchmark 推断并发性能。
- 长上下文请求和短请求混跑，短请求被拖慢。
- GPU 空闲但 CPU tokenizer 或网络成为瓶颈。

## 参考资料

- [vLLM docs](https://docs.vllm.ai/en/latest/)
- [SGLang docs](https://docs.sglang.ai/)
- [NVIDIA CUDA C++ Programming Guide](https://docs.nvidia.com/cuda/cuda-c-programming-guide/)
- [[Token]]
- [[Context Window]]
