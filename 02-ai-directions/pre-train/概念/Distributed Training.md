# Distributed Training

Distributed Training 是把训练拆到多张 GPU 或多台机器上。它不是为了显得高级，而是因为模型、数据和训练时间超出了单卡能力。

核心问题是模型、数据和训练时间如何被拆到多张 GPU 或多台机器上。

## 核心问题

大模型训练会遇到三类限制：

- 模型权重和 optimizer state 放不下一张 GPU。
- batch 或序列太长，activation 占显存。
- 单卡训练太慢，需要更多计算吞吐。

分布式训练用不同并行方式缓解这些限制。

## 常见并行

- Data Parallel：每张卡放一份模型，处理不同 batch，再同步梯度。
- Tensor Parallel：把同一层矩阵计算切到多卡。
- Pipeline Parallel：把不同层放到不同卡，像流水线一样执行。
- FSDP / ZeRO：分片参数、梯度和 optimizer state。
- Expert Parallel：MoE 模型中按 expert 分布计算。

这些方法会带来通信成本和调试难度。并行不是免费加速。

## 工程形态

大规模训练系统要处理：

- 节点启动和通信拓扑。
- checkpoint 分片和恢复。
- 混合精度和数值稳定。
- 数据并行下的 shuffle 和 seed。
- 失败节点和任务重启。
- 吞吐、显存和网络监控。

## 常见失败模式

- 单卡能跑，多卡 loss 不一致。
- checkpoint 在不同并行配置下无法加载。
- 通信成为瓶颈，GPU 利用率低。
- pipeline bubble 导致吞吐不如预期。
- 随机种子和数据切分不稳定，实验不可复现。

## 参考资料

- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
- [Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)
- [DeepSpeed](https://www.deepspeed.ai/)
- [PyTorch FSDP](https://pytorch.org/docs/stable/fsdp.html)
