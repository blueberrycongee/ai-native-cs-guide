# Pre-Train 路径

Pre-Train 关注模型在大规模数据上学习通用能力的过程。它不是本仓库的第一实践路线，但需要有入口，避免把所有模型能力都归因于 prompt。

## 学习顺序

1. 先读 [[Transformer]]、[[Token]]、[[Embedding]]、[[Inference]]。
2. 读 [[Training Loop]]，用 nanoGPT 看最小 GPT 训练循环。
3. 理解 [[Dataset Pipeline]]、[[Tokenizer Training]]、batch、loss、[[Checkpoint]] 和生成评测。
4. 用 LitGPT 跑一个更接近现代 LLM 工作流的小实验。
5. 再看 [[Distributed Training]] 和 Megatron-LM，补并行和稳定性概念。

不要从训练平台开始。没有小规模训练直觉时，平台细节会变成术语堆积。

## 必做项目

做一个小语言模型训练实验：

- 准备一个小文本数据集。
- 训练一个字符级或 token 级模型。
- 保存配置、数据版本、loss 曲线和 checkpoint。
- 改变 context length 或数据清洗策略，比较生成样例。
- 写一页复盘：模型学到了什么，没学到什么，为什么。

这个项目不会训练出强模型。目标是理解预训练到底在优化什么。

## 参考项目

- [nanoGPT](https://github.com/karpathy/nanoGPT)：代表小而清楚的 GPT 训练实现。先看 `model.py`、`train.py`、`config/`。
- [LitGPT](https://github.com/Lightning-AI/litgpt)：代表更完整的 pretrain/finetune/evaluate/deploy 工作流。先看 workflows、recipes 和 examples。
- [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)：代表大规模 Transformer 训练。先看 README、training scripts、parallelism 文档。
- [Hugging Face Transformers Trainer](https://huggingface.co/docs/transformers/trainer)：看通用训练循环和 evaluation 接口。

更详细的阅读入口见 [[Pre-Train 信息来源]]。项目拆解见 [[Pre-Train 开源项目]]。工具分层见 [[Pre-Train 框架和工具]]。

## 工程实践清单

- 数据清洗和切分可复现。
- tokenizer 与训练、推理一致。
- 训练配置写入文件，不靠命令行历史。
- checkpoint、日志和样例输出能对应到同一次实验。
- 至少比较一次数据或上下文长度变化。
- 不只看 loss，也看生成样例和失败模式。

## 必学知识

必须能读代码：

- [[Transformer]]
- [[Token]]
- [[Embedding]]
- 训练循环、loss、optimizer、checkpoint

必须理解机制：

- 数据质量和模型行为的关系。
- context length 对训练和推理的影响。
- 预训练和 [[Post-Train]] 解决的问题不同。
- 评测不能只看训练 loss。

知道存在即可：

- 数据去重和污染检测。
- 混合数据配比。
- 张量并行、流水线并行、FSDP。
- 大规模训练稳定性。

## 阶段验收标准

- 能从源码解释一次 batch 如何变成 loss。
- 能复现实验并得到相近 loss 曲线。
- 能说明生成样例的变化来自数据、模型大小还是训练设置。
- 能估算一次实验的 GPU 时间和失败重跑成本。
- 能说清自己的小实验和真实预训练系统差在哪里。

## 继续深入

深入顺序建议是：LitGPT recipes -> Megatron-LM parallelism -> 数据治理 -> 训练稳定性 -> 大规模 eval。别急着跳到千卡训练，先把可复现的小实验做扎实。
