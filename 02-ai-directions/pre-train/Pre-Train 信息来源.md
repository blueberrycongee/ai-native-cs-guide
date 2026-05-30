# Pre-Train 信息来源

Pre-Train 的资料入口分成教学实现、训练框架、大规模系统和论文几类。小项目展示训练循环，大项目展示工程取舍，论文提供结构和系统问题的原始语境。

## 教学实现

- [nanoGPT](https://github.com/karpathy/nanoGPT)：GPT 模型结构、训练循环、数据加载、optimizer 和 checkpoint。
- [build-nanogpt](https://github.com/karpathy/build-nanogpt)：从零搭建 GPT 的讲解和代码。

## 框架和官方文档

- [LitGPT](https://github.com/Lightning-AI/litgpt)：pretrain、finetune、evaluate、deploy 的完整工作流。
- [Hugging Face Transformers Trainer](https://huggingface.co/docs/transformers/trainer)：通用训练和 evaluation loop。
- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)：大规模 Transformer 训练和并行策略。
- [Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)：Megatron Core 的组件化训练能力。

## 论文

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：[[Transformer]] 结构。
- [Language Models are Few-Shot Learners](https://arxiv.org/abs/2005.14165)：规模、数据和 few-shot 能力的关系。
- [The Pile](https://arxiv.org/abs/2101.00027)：预训练数据集组织。
- [Megatron-LM paper](https://arxiv.org/abs/1909.08053)：模型并行和大规模训练系统。

## 社区讨论

Reddit、Discord、GitHub Issues 常出现数据质量、GPU 不够、loss 不降、checkpoint 恢复失败、tokenizer 不一致等真实限制。
