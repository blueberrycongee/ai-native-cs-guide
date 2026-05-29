# Post-Train 框架和工具

Post-Train 工具要按训练方法和项目阶段选择。不要因为一个工具支持很多方法，就把所有方法都学一遍。

## 必须会用

- TRL：能跑 SFT 和 DPO，能理解 trainer 的 dataset 输入。
- PEFT：理解 LoRA/QLoRA 的基本使用和限制。
- Hugging Face Datasets：能准备 train/eval 数据，避免格式混乱。
- 一个 eval 脚本：能比较训练前后同一批样例。

## 必须理解

- Axolotl：理解配置驱动 fine-tuning，能读懂 YAML。
- LLaMA-Factory：理解 WebUI 背后的数据格式和训练参数。
- chat template：训练和推理格式必须一致。
- vLLM：做 GRPO/RLHF 时常用于快速 rollout。

## 后续深入

- OpenRLHF：大规模 RLHF、GRPO、Ray、vLLM。
- DeepSpeed、FSDP：多 GPU 训练和显存优化。
- reward model 训练工具：只有真的做 RLHF 时再深入。
- 数据标注和偏好采集工具：真实 post-training 的瓶颈常在这里。

## 学习顺序

1. 用 TRL 跑 SFT。
2. 用同一 eval 集比较基础模型和 SFT 模型。
3. 准备 preference pairs，跑 DPO。
4. 用 Axolotl 复现实验配置。
5. 再看 GRPO、OpenRLHF 和分布式 rollout。

工具选择要服从问题。不要为了使用 RLHF 而制造一个本来 SFT 或 RAG 能解决的问题。
