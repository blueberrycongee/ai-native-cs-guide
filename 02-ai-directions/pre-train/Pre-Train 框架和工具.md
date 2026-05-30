# Pre-Train 框架和工具

Pre-Train 工具可以按训练系统的层次整理：张量计算、训练循环、数据、实验记录和分布式训练。

## 张量计算和训练循环

- PyTorch：`forward`、loss、backward、optimizer、scheduler 和 autograd。
- nanoGPT：紧凑的 GPT 训练实现，涉及模型结构、数据加载、训练循环和 checkpoint。
- LitGPT：覆盖 pretrain、finetune、evaluate、serve 的工作流，涉及 recipes 和实验配置。
- Hugging Face Transformers Trainer：通用训练抽象、training arguments、evaluation loop 和 checkpoint。

## 数据和 tokenizer

- Hugging Face Datasets：streaming、map、shuffle、train/test split 和数据版本。
- Hugging Face Tokenizers：tokenizer 训练、加载和模型输入一致性。
- 数据清洗工具链：去重、过滤、格式化、打包和版本记录。

## 实验记录

- Weights & Biases：训练日志、loss 曲线、样例记录和实验对比。
- TensorBoard：loss、learning rate、gradient 和样例可视化。
- checkpoint 存储：模型参数、optimizer state、scheduler state、随机状态和配置。

## 分布式训练

- Megatron-LM / Megatron Core：大规模 Transformer 训练、张量并行、流水线并行和 distributed optimizer。
- DeepSpeed：ZeRO、分布式训练和大模型内存优化。
- PyTorch FSDP：全分片数据并行。
- Accelerate：多设备训练入口和配置封装。
