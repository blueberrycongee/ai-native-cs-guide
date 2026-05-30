# Post-Train 信息来源

Post-Train 的资料入口围绕训练脚本、数据格式、评测、偏好优化和大规模 RLHF/GRPO 展开。官方文档、论文和项目配置通常要一起看，单独的微调结果截图信息量很低。

## 官方文档和项目文档

- [TRL docs](https://huggingface.co/docs/trl)：[[SFT]]、[[DPO]]、[[GRPO]]、[[Reward Model]] 的 trainer 和 CLI。
- [Axolotl docs](https://docs.axolotl.ai/)：配置化 fine-tuning、数据格式、LoRA/QLoRA、DPO、GRPO 和多 GPU 配置。
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)：多模型微调、WebUI、数据格式和训练脚本。
- [OpenRLHF docs](https://openrlhf.readthedocs.io/)：Ray、vLLM、PPO、GRPO、REINFORCE++ 等大规模 RLHF/agent RL 训练。

## 论文

- [InstructGPT](https://arxiv.org/abs/2203.02155)：[[RLHF]] 和人类偏好数据。
- [Direct Preference Optimization](https://arxiv.org/abs/2305.18290)：[[DPO]] 和显式 RL pipeline 之外的偏好优化。
- [OpenRLHF paper](https://arxiv.org/abs/2405.11143)：Ray、vLLM、DeepSpeed 组织大规模 RLHF。
- [LLaMA-Factory paper](https://arxiv.org/abs/2403.13372)：统一微调工具的工程取舍。
- [DeepSeekMath](https://arxiv.org/abs/2402.03300)：[[GRPO]] 的公式和 group-relative advantage。
- [DAPO](https://dapo-sia.github.io/static/pdf/dapo_paper.pdf)：长 CoT RLVR 的系统和稳定性改造。
- [GSPO](https://arxiv.org/abs/2507.18071)：sequence-level policy optimization 与 MoE RL 稳定性。
- [DeepSeek-R1](https://github.com/deepseek-ai/DeepSeek-R1)：R1-Zero、R1 多阶段 pipeline 和 distillation。
- [Qwen3 Technical Report](https://arxiv.org/abs/2505.09388)：thinking/non-thinking、MoE、多语言和后训练策略。

## 数据和社区问题

Post-training 项目里的关键问题通常落在数据上：instruction 任务分布、preference 标注规则、拒答样例、安全样例、工具调用样例、chat template，以及训练集和 eval 集是否泄漏。

社区讨论常出现硬件需求、数据格式、模型模板、LoRA rank 和 batch 设置经验。这些信息更像问题线索，最终仍要回到 eval 和配置复现。
