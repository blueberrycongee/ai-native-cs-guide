# Post-Train 开源项目

Post-Train 项目可以按训练方法、配置方式、数据格式和评测闭环来看。

## TRL

- GitHub：[huggingface/trl](https://github.com/huggingface/trl)
- Docs：[TRL docs](https://huggingface.co/docs/trl)
- 类型：Hugging Face 生态里的 post-training library
- 代表形态：SFT、DPO、GRPO、reward modeling 等 trainer 接在 Transformers、Datasets、PEFT、vLLM 和 Accelerate 生态上。
- 相关主题：SFTTrainer、DPOTrainer、GRPOTrainer、dataset formats、CLI、examples。

## Axolotl

- GitHub：[axolotl-ai-cloud/axolotl](https://github.com/axolotl-ai-cloud/axolotl)
- Docs：[Axolotl docs](https://docs.axolotl.ai/)
- 类型：配置驱动的 fine-tuning 工具
- 代表形态：用 YAML 描述数据、模型、LoRA、训练、评测和推理。
- 相关主题：dataset formats、examples、fine-tuning method、LoRA/QLoRA、DPO、GRPO、reward modeling。

## LLaMA-Factory

- GitHub：[hiyouga/LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)
- 类型：多模型微调和 WebUI 工具
- 代表形态：多模型、多方法和 WebUI 的 fine-tuning 工作流。
- 相关主题：SFT、DPO、PPO、KTO、ORPO、data format、training scripts、WebUI 配置。

## OpenRLHF

- GitHub：[OpenRLHF/OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)
- Docs：[OpenRLHF docs](https://openrlhf.readthedocs.io/)
- 类型：大规模 RLHF / GRPO / agent RL 训练框架
- 代表形态：Ray、vLLM、DeepSpeed 进入 post-training pipeline。
- 相关主题：online generation、actor、critic、reward、reference model、RL training guide、Ray + vLLM 配置。
