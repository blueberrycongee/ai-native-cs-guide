# Pre-Train 信息来源

Pre-Train 的信息源要分清“教学项目”“训练框架”“大规模系统”和“论文”。初学者最容易被大规模训练术语带偏，结果连一次 batch 如何变成 loss 都说不清。

## 教学项目

- [nanoGPT](https://github.com/karpathy/nanoGPT)：最适合先看训练循环。重点读 `model.py`、`train.py`、`data/` 和 `config/`。
- [build-nanogpt](https://github.com/karpathy/build-nanogpt)：配合讲解视频看 GPT 从零搭建。

这类资料的价值是清楚，不是生产完整。不要用它们判断大规模训练工程。

## 框架和官方文档

- [LitGPT](https://github.com/Lightning-AI/litgpt)：看 pretrain、finetune、evaluate、deploy 的完整工作流。
- [Hugging Face Transformers Trainer](https://huggingface.co/docs/transformers/trainer)：看通用训练和 evaluation loop。
- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)：看大规模 Transformer 训练和并行策略。
- [Megatron Core docs](https://docs.nvidia.com/megatron-core/developer-guide/latest/get-started/quickstart.html)：看 Megatron Core 的组件化训练能力。

## 论文

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762)：理解 [[Transformer]] 结构。
- [Language Models are Few-Shot Learners](https://arxiv.org/abs/2005.14165)：看规模、数据和 few-shot 能力的关系。
- [The Pile](https://arxiv.org/abs/2101.00027)：看预训练数据集如何组织。
- [Megatron-LM paper](https://arxiv.org/abs/1909.08053)：看模型并行为什么出现。

论文先看问题设置、系统图和实验结论，不要一开始卡在细节公式。

## 社区讨论

Reddit、Discord、GitHub Issues 能看到真实限制：数据质量、GPU 不够、loss 不降、checkpoint 恢复失败、tokenizer 不一致。它们适合发现问题，不适合直接当最佳实践。

每次采用社区建议，都要回到代码和实验验证。
