# Pre-Train 路径

Pre-Train 关注模型如何从大规模数据中学习通用表示和生成能力。小规模训练项目能看清 batch、loss、optimizer、checkpoint 和生成样例；大规模训练系统会把问题扩展到数据治理、并行、通信、容错和成本。

## 训练链路

一个预训练系统通常包含这些环节：

```text
raw data
  -> cleaning / dedup / filtering
  -> tokenizer
  -> packing and dataloader
  -> transformer training loop
  -> checkpoint
  -> samples and evals
  -> distributed training
```

相关概念包括 [[Transformer]]、[[Token]]、[[Embedding]]、[[Training Loop]]、[[Dataset Pipeline]]、[[Tokenizer Training]]、[[Checkpoint]] 和 [[Distributed Training]]。

## 系统组件

- 数据管线：清洗、去重、过滤、配比、版本和污染控制。
- tokenizer：把文本映射成 token，并影响训练和推理的一致性。
- 训练循环：forward、loss、backward、optimizer、scheduler、gradient accumulation。
- checkpoint：保存模型、优化器、随机状态和训练配置。
- 评测：loss、生成样例、下游任务和数据切分。
- 分布式训练：数据并行、张量并行、流水线并行、FSDP 和容错。

## 代表项目

- [nanoGPT](https://github.com/karpathy/nanoGPT)：紧凑的 GPT 训练实现，相关主题包括模型结构、训练循环、数据加载和 checkpoint。
- [LitGPT](https://github.com/Lightning-AI/litgpt)：覆盖 pretrain、finetune、evaluate 和 serve 的训练工具，相关主题包括 recipes、workflows 和实验配置。
- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)：大规模 Transformer 训练参考实现，相关主题包括并行策略、distributed optimizer 和 checkpoint。
- [Hugging Face Transformers Trainer](https://huggingface.co/docs/transformers/trainer)：通用训练循环和 evaluation 接口。

资料入口见 [[Pre-Train 信息来源]]，项目入口见 [[Pre-Train 开源项目]]，工具生态见 [[Pre-Train 框架和工具]]。
