# Post-Train 路径

Post-Train 关注预训练之后的模型改造：指令微调、偏好优化、工具使用数据、拒答策略和评测。它连接基础模型和真实产品体验。

## 学习顺序

1. 先理解 [[Pre-Train]] 和 post-train 的边界。
2. 用小模型做 [[SFT]]，学习数据格式、chat template 和训练配置。
3. 用同一组 [[Evals]] 比较微调前后输出。
4. 再看 [[DPO]]、[[GRPO]]、[[Reward Model]] 和 [[RLHF]]。
5. 最后看大规模框架如何处理分布式、vLLM、Ray 和数据管线。

公式可以晚一点看。先知道每种方法想修什么问题。

## 必做项目

做一个小模型指令微调实验：

- 选择一个小模型和一小批任务样例。
- 准备训练集、验证集和 eval 集。
- 跑 SFT，保存配置、数据版本和 checkpoint。
- 微调前后跑同一组 eval。
- 记录哪些样例变好，哪些退化。

进阶可以做 DPO：准备偏好对，比较 SFT 模型和 DPO 模型在同一任务上的变化。

## 参考项目

- [TRL](https://huggingface.co/docs/trl)：代表 Hugging Face 生态里的 SFT、DPO、GRPO、reward model。先看 quickstart、SFTTrainer、DPOTrainer、GRPOTrainer。
- [Axolotl](https://docs.axolotl.ai/)：代表配置驱动的 fine-tuning。先看 quick start、dataset formats、examples。
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)：代表多模型、多方法和 WebUI 的 fine-tuning 工作流。先看 examples、data format、training scripts。
- [OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)：代表 Ray、vLLM 和 RLHF/GRPO 工程化。先看 README、examples 和 docs。

更详细的信息源见 [[Post-Train 信息来源]]。项目拆解见 [[Post-Train 开源项目]]。工具分层见 [[Post-Train 框架和工具]]。

## 工程实践清单

- 数据样例能追溯来源和许可。
- chat template 与训练、推理一致。
- 微调前后跑同一组 eval。
- 保存训练配置、随机种子、模型版本和数据版本。
- 区分格式遵循、事实正确、拒答和安全边界。
- 不把 loss 降低等同于用户体验变好。

## 必学知识

必须能操作：

- SFT 数据格式。
- LoRA/QLoRA 基本训练流程。
- eval 集构建和结果对比。

必须理解机制：

- instruction tuning 让模型更会按任务回答。
- preference optimization 改变偏好，不凭空增加事实知识。
- RLHF 和 [[RL]] 有联系，但产品侧仍要做权限和校验。
- 微调、[[RAG]]、prompt 和工具调用是不同手段。

知道存在即可：

- reward model。
- PPO、GRPO、online RL。
- 分布式后训练。

## 阶段验收标准

- 能解释 SFT 数据如何进入训练。
- 能用 eval 说明微调带来的提升和退化。
- 能判断一个问题该用 RAG、prompt、微调还是产品逻辑解决。
- 能复现实验配置。
- 能说清自己的小实验和真实 post-training pipeline 差在哪里。

## 继续深入

继续深入时，先在 TRL 里跑通 SFT 和 DPO，再用 Axolotl 或 LLaMA-Factory 做配置化实验。只有当你真的需要大规模 RLHF/GRPO，再看 OpenRLHF。
