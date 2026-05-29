# Pre-Train 框架和工具

Pre-Train 工具按学习阶段看，不按名气看。先用能读懂的工具建立训练直觉，再进入更复杂的框架。

## 必须会用

- PyTorch：能写一个最小训练循环，理解 `forward`、loss、backward、optimizer、scheduler。
- nanoGPT：能跑通小数据训练，能解释 `model.py` 和 `train.py`。
- Hugging Face Tokenizers 或等价 tokenizer 工具：能训练或加载 tokenizer，知道 tokenizer 不一致会影响训练和推理。

## 必须理解

- LitGPT：理解 recipes、pretrain、finetune、evaluate、serve 的完整工作流。
- Hugging Face Transformers Trainer：理解通用训练抽象，知道它方便在哪里，也知道它隐藏了哪些细节。
- Hugging Face Datasets：理解 streaming、map、shuffle、train/test split 和数据版本。
- Weights & Biases、TensorBoard 或等价工具：理解训练日志、loss 曲线和样例记录。

## 后续深入

- Megatron-LM / Megatron Core：大规模 Transformer 训练。
- DeepSpeed：分布式训练、ZeRO 和大模型内存优化。
- PyTorch FSDP：全分片数据并行。
- Accelerate：多设备训练入口。

## 学习顺序

1. 手写最小 PyTorch loop。
2. 读 nanoGPT。
3. 用 LitGPT 跑 pretrain recipe。
4. 用 Trainer 理解通用训练接口。
5. 再看 Megatron-LM、DeepSpeed、FSDP。

如果连小实验的 loss、checkpoint 和生成样例都不会解释，不要急着学千卡训练。
