# Post-Train 框架和工具

Post-Train 工具围绕训练方法、数据格式、偏好优化、评测和大规模 rollout 展开。

## SFT 和偏好优化

- TRL：SFT、DPO、GRPO、reward model 和 trainer 抽象。
- PEFT：LoRA、QLoRA 和参数高效微调。
- Axolotl：配置驱动的 fine-tuning 工作流，相关主题包括 YAML、dataset formats 和复现实验。
- LLaMA-Factory：多模型、多方法和 WebUI 的 fine-tuning 工作流。

## 数据和格式

- Hugging Face Datasets：train/eval 数据准备、map、shuffle、split 和数据版本。
- chat template：训练和推理格式的一致性。
- 偏好数据工具：chosen/rejected pair、评分样例、拒答样例和安全样例。

## 评测

- eval 脚本：比较训练前后同一批样例。
- LLM-as-judge：自动评分和错误分类。
- 结构化检查：JSON schema、工具调用格式、拒答策略和安全边界。

## 大规模 RLHF / GRPO

- OpenRLHF：Ray、vLLM、RLHF 和 GRPO 工程化。
- vLLM：rollout 阶段的高吞吐推理。
- DeepSpeed / FSDP：多 GPU 训练和显存优化。
- verl：actor、rollout、reference、reward worker 和 trainer 的分布式数据流。
