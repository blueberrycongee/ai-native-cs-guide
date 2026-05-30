# Post-Train 路径

Post-Train 把预训练模型改造成更贴近任务和产品场景的模型。它围绕数据、训练方法、偏好、奖励、评测和推理格式展开，连接 [[Pre-Train]] 与真实应用体验。

## 训练链路

一个常见 post-training 系统会出现这些环节：

```text
base model
  -> instruction data
  -> SFT
  -> eval
  -> preference data
  -> DPO / RLHF / GRPO
  -> eval and deployment comparison
```

相关概念包括 [[SFT]]、[[Preference Optimization]]、[[DPO]]、[[Reward Model]]、[[RLHF]]、[[GRPO]] 和 [[Evals]]。

## 系统组件

- 数据格式：instruction、chat template、工具调用样例、拒答样例和偏好对。
- 训练方法：SFT、DPO、reward model、PPO、GRPO。
- 训练配置：LoRA/QLoRA、batch、learning rate、sequence length、模板和 checkpoint。
- 评测：格式遵循、事实、拒答、安全、任务成功率和回归样例。
- 推理对比：同一组输入在训练前后产生的行为差异。
- 大规模 RLHF / GRPO：rollout、reward/verifier、reference model、actor、Ray worker 和 vLLM/SGLang 推理后端。

## 代表项目

- [TRL](https://huggingface.co/docs/trl)：Hugging Face 生态里的 SFT、DPO、GRPO 和 reward model 工具。
- [Axolotl](https://docs.axolotl.ai/)：配置驱动的 fine-tuning 工作流，相关主题包括 dataset formats、YAML 配置和训练复现。
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)：多模型、多方法和 WebUI 的 fine-tuning 工作流。
- [OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)：Ray、vLLM 和 RLHF/GRPO 工程化实现。

资料入口见 [[Post-Train 信息来源]]，项目入口见 [[Post-Train 开源项目]]，工具生态见 [[Post-Train 框架和工具]]。
