# Post-Train 信息来源

Post-Train 的信息更新很快。要优先看官方文档、训练脚本、数据格式和 eval，而不是只看“某模型微调成功”的帖子。

## 官方文档和项目文档

- [TRL docs](https://huggingface.co/docs/trl)：看 [[SFT]]、[[DPO]]、[[GRPO]]、[[Reward Model]] 的 trainer 和 CLI。
- [Axolotl docs](https://docs.axolotl.ai/)：看配置化 fine-tuning、数据格式、LoRA/QLoRA、DPO、GRPO 和多 GPU 配置。
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)：看多模型微调、WebUI、数据格式和训练脚本。
- [OpenRLHF docs](https://openrlhf.readthedocs.io/)：看 Ray、vLLM、PPO、GRPO、REINFORCE++ 等大规模 RLHF/agent RL 训练。

## 论文

- [InstructGPT](https://arxiv.org/abs/2203.02155)：理解 [[RLHF]] 如何让模型更符合人类偏好。
- [Direct Preference Optimization](https://arxiv.org/abs/2305.18290)：理解 [[DPO]] 为什么绕开显式 RL pipeline。
- [OpenRLHF paper](https://arxiv.org/abs/2405.11143)：看大规模 RLHF 工程如何用 Ray、vLLM、DeepSpeed 组织。
- [LLaMA-Factory paper](https://arxiv.org/abs/2403.13372)：看统一微调工具的工程取舍。
- [DeepSeekMath](https://arxiv.org/abs/2402.03300)：看 [[GRPO]] 的原始公式和 group-relative advantage。
- [DAPO](https://dapo-sia.github.io/static/pdf/dapo_paper.pdf)：看长 CoT RLVR 的可复现系统和稳定性改造。
- [GSPO](https://arxiv.org/abs/2507.18071)：看 sequence-level policy optimization 与 MoE RL 稳定性。
- [DeepSeek-R1](https://github.com/deepseek-ai/DeepSeek-R1)：看 R1-Zero、R1 多阶段 pipeline 和 distillation。
- [Qwen3 Technical Report](https://arxiv.org/abs/2505.09388)：看 thinking/non-thinking、MoE、多语言和后训练策略。

## 数据来源

看 post-training 项目时要特别关注数据：

- instruction 数据的任务分布。
- preference 数据的标注规则。
- 拒答、安全和工具调用样例。
- chat template 是否和推理时一致。
- 训练集和 eval 集是否泄漏。

数据质量比训练命令更重要。坏数据会稳定地产生坏行为。

## 社区讨论

社区讨论适合发现硬件需求、数据格式坑、某模型模板问题、LoRA rank 和 batch 设置经验。但这些建议必须回到 eval 验证。微调成功截图不是证据。
