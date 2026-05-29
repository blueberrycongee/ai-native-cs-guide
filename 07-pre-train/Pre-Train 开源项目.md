# Pre-Train 开源项目

Pre-Train 方向看项目时，要按规模分层。小项目用来理解机制，大项目用来理解工程取舍。不要拿 nanoGPT 的简洁要求 Megatron-LM，也不要拿 Megatron-LM 的复杂度吓退入门实验。

## nanoGPT

- GitHub：[karpathy/nanoGPT](https://github.com/karpathy/nanoGPT)
- 类型：教学型 GPT 训练实现

学习价值：

- 一个文件里看清 GPT 模型结构。
- 训练脚本足够短，能追踪 data、batch、loss、optimizer、checkpoint。
- 适合先跑 Tiny Shakespeare，再换自己的小数据。

先看：

- `model.py`
- `train.py`
- `data/shakespeare_char/`
- `config/train_shakespeare_char.py`

限制：

- 不是生产训练框架。
- 分布式、数据治理、容错、监控都很弱。

## LitGPT

- GitHub：[Lightning-AI/litgpt](https://github.com/Lightning-AI/litgpt)
- 类型：中等复杂度的 LLM 训练和部署工具

学习价值：

- 同时覆盖 pretrain、finetune、evaluate、serve。
- recipes 比纯脚本更接近真实实验管理。
- 适合从教学代码过渡到工程代码。

先看：

- README 的 workflows。
- `litgpt/` 里的 model、data、scripts。
- pretrain 和 finetune recipes。
- examples 和 tutorials。

## Megatron-LM

- GitHub：[NVIDIA/Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
- Docs：[Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)
- 类型：大规模 Transformer 训练参考实现

学习价值：

- 看到张量并行、流水线并行、数据并行、专家并行如何进入训练系统。
- 理解大规模训练不只是更大的 batch，而是通信、显存、checkpoint、容错和吞吐。

先看：

- README 的 Megatron-LM 与 Megatron Core 区分。
- training examples。
- parallelism 文档。
- 与 checkpoint、distributed optimizer 相关的说明。

限制：

- 入门成本高。
- 没有小模型训练直觉时，很容易只背并行术语。

## Hugging Face Transformers

- GitHub：[huggingface/transformers](https://github.com/huggingface/transformers)
- Docs：[Trainer](https://huggingface.co/docs/transformers/trainer)
- 类型：通用模型训练和评测生态

学习价值：

- 理解 Trainer 抽象、dataset、data collator、metrics、checkpoint。
- 和 Hugging Face Datasets、Accelerate、Hub 连接紧密。

先看：

- Trainer docs。
- causal language modeling examples。
- training arguments。
- checkpoint 和 resume 相关文档。
