# Pre-Train 开源项目

Pre-Train 项目可以按规模和抽象层次看。nanoGPT 展示最小训练循环，LitGPT 展示更完整的实验工作流，Megatron-LM 展示大规模训练系统，Transformers Trainer 展示通用训练抽象。

## nanoGPT

- GitHub：[karpathy/nanoGPT](https://github.com/karpathy/nanoGPT)

nanoGPT 是教学型 GPT 训练实现。它用单文件模型结构和简短训练脚本呈现 `model.py`、`train.py`、data、batch、loss、optimizer、checkpoint 之间的关系。它不覆盖完整生产训练框架里的数据治理、容错、监控和大规模并行。

## LitGPT

- GitHub：[Lightning-AI/litgpt](https://github.com/Lightning-AI/litgpt)

LitGPT 是中等复杂度的 LLM 训练和部署工具，把 pretrain、finetune、evaluate、serve 放在同一套 workflow 里。recipes、workflows、model、data、scripts、examples 和 tutorials 都围绕实验组织展开。

## Megatron-LM

- GitHub：[NVIDIA/Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
- Docs：[Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)

Megatron-LM 是大规模 Transformer 训练参考实现。张量并行、流水线并行、数据并行、专家并行、training examples、parallelism、checkpoint、distributed optimizer、通信和吞吐，都能在这个项目里看到。

## Hugging Face Transformers

- GitHub：[huggingface/transformers](https://github.com/huggingface/transformers)
- Docs：[Trainer](https://huggingface.co/docs/transformers/trainer)

Hugging Face Transformers 是通用模型训练和评测生态。Trainer、dataset、data collator、metrics、checkpoint、Hub、causal language modeling examples、training arguments 和 resume 机制都属于这个生态的一部分。
