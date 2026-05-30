# Pre-Train 开源项目

Pre-Train 项目可以按规模和抽象层次看。nanoGPT 展示最小训练循环，LitGPT 展示更完整的实验工作流，Megatron-LM 展示大规模训练系统，Transformers Trainer 展示通用训练抽象。

## nanoGPT

- GitHub：[karpathy/nanoGPT](https://github.com/karpathy/nanoGPT)
- 类型：教学型 GPT 训练实现
- 代表形态：单文件模型结构和简短训练脚本。
- 相关主题：`model.py`、`train.py`、data、batch、loss、optimizer、checkpoint。
- 边界：不覆盖完整生产训练框架里的数据治理、容错、监控和大规模并行。

## LitGPT

- GitHub：[Lightning-AI/litgpt](https://github.com/Lightning-AI/litgpt)
- 类型：中等复杂度的 LLM 训练和部署工具
- 代表形态：pretrain、finetune、evaluate、serve 放在同一套 workflow 里。
- 相关主题：recipes、workflows、model、data、scripts、examples、tutorials。

## Megatron-LM

- GitHub：[NVIDIA/Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
- Docs：[Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)
- 类型：大规模 Transformer 训练参考实现
- 代表形态：张量并行、流水线并行、数据并行、专家并行进入同一个训练系统。
- 相关主题：training examples、parallelism、checkpoint、distributed optimizer、通信和吞吐。

## Hugging Face Transformers

- GitHub：[huggingface/transformers](https://github.com/huggingface/transformers)
- Docs：[Trainer](https://huggingface.co/docs/transformers/trainer)
- 类型：通用模型训练和评测生态
- 代表形态：Trainer、dataset、data collator、metrics、checkpoint 和 Hub 生态。
- 相关主题：causal language modeling examples、training arguments、checkpoint、resume。
