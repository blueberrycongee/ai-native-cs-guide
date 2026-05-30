# Post-Train 开源项目

Post-Train 项目可以按训练方法、配置方式、数据格式和评测闭环来看。

## TRL

- GitHub：[huggingface/trl](https://github.com/huggingface/trl)
- Docs：[TRL docs](https://huggingface.co/docs/trl)

TRL 是 Hugging Face 生态里的 post-training library。SFT、DPO、GRPO、reward modeling 等 trainer 接在 Transformers、Datasets、PEFT、vLLM 和 Accelerate 生态上，SFTTrainer、DPOTrainer、GRPOTrainer、dataset formats、CLI 和 examples 都在这条线上。

## Axolotl

- GitHub：[axolotl-ai-cloud/axolotl](https://github.com/axolotl-ai-cloud/axolotl)
- Docs：[Axolotl docs](https://docs.axolotl.ai/)

Axolotl 是配置驱动的 fine-tuning 工具，用 YAML 描述数据、模型、LoRA、训练、评测和推理。dataset formats、examples、fine-tuning method、LoRA/QLoRA、DPO、GRPO 和 reward modeling 都通过配置组织起来。

## LLaMA-Factory

- GitHub：[hiyouga/LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)

LLaMA-Factory 是多模型微调和 WebUI 工具。它把 SFT、DPO、PPO、KTO、ORPO、data format、training scripts 和 WebUI 配置放在一个面向多模型的 fine-tuning 工作流里。

## OpenRLHF

- GitHub：[OpenRLHF/OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)
- Docs：[OpenRLHF docs](https://openrlhf.readthedocs.io/)

OpenRLHF 是大规模 RLHF / GRPO / agent RL 训练框架。Ray、vLLM、DeepSpeed、online generation、actor、critic、reward、reference model、RL training guide 和 Ray + vLLM 配置，是它呈现出的主要工程问题。
