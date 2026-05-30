# Post-Train 开源项目

Post-Train 项目要看三件事：支持哪些训练方法，数据格式如何表达，评测和推理如何闭环。

## TRL

- GitHub：[huggingface/trl](https://github.com/huggingface/trl)
- Docs：[TRL docs](https://huggingface.co/docs/trl)
- 类型：Hugging Face 生态里的 post-training library

学习价值：

- Trainer 覆盖 SFT、DPO、GRPO、reward modeling 等方法。
- 和 Transformers、Datasets、PEFT、vLLM、Accelerate 生态连接紧。
- 适合先读方法级 trainer 的输入输出。

先看：

- SFTTrainer、DPOTrainer、GRPOTrainer。
- Dataset formats。
- CLI。
- examples。

## Axolotl

- GitHub：[axolotl-ai-cloud/axolotl](https://github.com/axolotl-ai-cloud/axolotl)
- Docs：[Axolotl docs](https://docs.axolotl.ai/)
- 类型：配置驱动的 fine-tuning 工具

学习价值：

- YAML 配置覆盖数据、模型、LoRA、训练、评测和推理。
- 支持多种模型、VLM、LoRA/QLoRA、DPO、GRPO、reward modeling。
- 适合学习可复现实验配置。

先看：

- Quick start。
- dataset formats。
- examples。
- choosing fine-tuning method。

## LLaMA-Factory

- GitHub：[hiyouga/LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)
- 类型：多模型微调和 WebUI 工具

学习价值：

- 适合观察低门槛 fine-tuning 工作流。
- 覆盖 SFT、DPO、PPO、KTO、ORPO 等方法。
- WebUI 对初学者友好，但不要只点按钮，要看生成的配置和数据格式。

先看：

- README。
- examples。
- data format。
- training scripts。

## OpenRLHF

- GitHub：[OpenRLHF/OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)
- Docs：[OpenRLHF docs](https://openrlhf.readthedocs.io/)
- 类型：大规模 RLHF / GRPO / agent RL 训练框架

学习价值：

- 展示 Ray、vLLM、DeepSpeed 如何进入 post-training。
- 适合理解 online generation、actor、critic、reward、reference model 的资源调度。

先看：

- README features。
- RL training guide。
- examples。
- Ray + vLLM 配置。

入门不要从 OpenRLHF 开始。先跑 TRL 或 Axolotl 的小实验，再回来读它。
