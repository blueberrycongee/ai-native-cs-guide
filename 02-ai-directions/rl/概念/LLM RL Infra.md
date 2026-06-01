# LLM RL Infra

大模型强化学习后训练的真实工程问题，不是 GRPO 公式本身，而是把算法公式、rollout 数据、训练框架、推理后端、MoE/并行优化和 eval 连接成一条稳定的系统链路。任何一个环节的偏差都会让训练静默变坏：reward 上升、eval 退化的反模式几乎都来自链路某处的隐式不一致。

这篇文档针对 RLVR、长 CoT、verl/DAPO/GSPO、熵崩溃、MoE RL 训推不一致和 DeepSeek/Qwen 系列训练叙事，给出系统视角的工程笔记。算法层面的 GRPO 推导放在 [[GRPO]]，本篇不重复。

## 系统链路

一个典型 LLM RL 训练系统可以拆成五段：

```text
prompt dataset
  -> rollout engine: vLLM/SGLang/transformers generate multiple responses
  -> reward/verifier: rule reward, reward model, tool/env result
  -> trainer: GRPO/PPO/DAPO/GSPO loss, KL, entropy, clipping
  -> evaluation: held-out tasks, behavior traces, cost/latency/regression
```

工程讨论里只说"用了 GRPO"信息量很低。真正决定训练能不能收敛、能不能泛化的，是这些系统细节：

- rollout 由谁生成、生成时的 policy 是什么版本。
- buffer 保存了哪些字段、字段间是否同步。
- trainer 怎么用 old logprobs 算 importance ratio。
- reward 和 eval 是否同源，是否会导致 reward hacking。
- 长输出、padding、sequence parallel、MoE routing、训练/推理精度差异会不会改变训练稳定性。

## GRPO 是不是 on-policy

标准 GRPO 更接近 on-policy。DeepSeekMath 论文把它描述为 PPO 的变体：不训练 critic，而是用同一 prompt 下多条 response 的组内 reward 估计 baseline，从而降低 PPO 的训练资源开销。

但它不是"完全没有旧数据"。GRPO 公式里的 $\pi_{\theta_{old}}$ 是当前更新轮次开始前冻结的行为策略：它生成样本，$\pi_\theta$ 在这些样本上更新。只要样本很新、staleness 很低，这仍然属于 on-policy / near-on-policy 训练。

如果 rollout engine 和 training engine 不同步，或者异步系统复用较旧的 rollout，问题就会转成 off-policy。TRL 文档明确把 vLLM 推理策略和训练策略不一致称为会把 on-policy 问题变成 off-policy 问题；NeMo RL 和 OpenRLHF 也都暴露了重要性采样校正、off-policy correction 或异步 rollout 的配置。

所以"GRPO 是 on-policy"和"GRPO 是 off-policy"两种说法都不准确。更准确的工程表述是：GRPO 的设计目标是 on-policy，但实际训练系统很容易因为 rollout 滞后、权重同步延迟、buffer 复用而滑向 off-policy 区间。

## GRPO 公式速览

完整推导和参数解释见 [[GRPO]]。本节只标记几个易错点。

GRPO 常见 objective：

$$
J_{GRPO}(\theta)=
\mathbb{E}_{q,\{o_i\}\sim\pi_{\theta_{old}}}
\left[
\frac{1}{G}\sum_{i=1}^{G}\frac{1}{|o_i|}\sum_{t=1}^{|o_i|}
\min
\left(
r_{i,t}(\theta)\hat A_{i,t},
\operatorname{clip}(r_{i,t}(\theta),1-\epsilon,1+\epsilon)\hat A_{i,t}
\right)
-\beta D_{KL}(\pi_\theta || \pi_{ref})
\right]
$$

几个工程上必须看清楚的项：

- $\pi_{\theta_{old}}$ 是行为策略，不一定是历史 replay buffer。判定 on/off-policy 取决于 staleness，不取决于符号名。
- $\hat A_i=(R_i-\operatorname{mean}(R_1,\ldots,R_G))/\operatorname{std}(R_1,\ldots,R_G)$ 是组内归一化。reward 整体上升时，组间相对信号可能消失，需要靠 dynamic sampling 或 reward shaping 维持信号。
- 长 CoT RLVR 里有些实现把 $\beta$ 设为 0，但这不是通用结论。KL 关闭后策略漂移更难控制，要靠 clipping 和 entropy 监控兜底。
- 重要性采样 ratio $\pi_\theta / \pi_{\theta_{old}}$ 偏离 1 越大，方差越高、clip 越频繁。

## GRPO batch 应该保存什么

buffer 字段不是"为了类而类"，它决定 trainer 能用哪些信号做修正。一个 GRPO batch 至少要保存：

- `prompts` / `input_ids`：原始 prompt 和 token。
- `responses` / `response_ids`：采样结果。
- `attention_mask` / `response_mask`：区分 prompt、response、padding、有效 token。任何一处错位都会让 loss 算到错的位置。
- `old_logprobs`：rollout policy 对 response token 的 log probability。
- `ref_logprobs`：reference model 的 log probability，用于 KL。
- `rewards`：规则 verifier、reward model 或环境返回。
- `advantages`：组内归一化后的 reward。
- `metadata`：答案、数据集来源、采样温度、长度、是否截断、工具调用 trace。

在 verl 这类框架里，还要关心 `DataProto` 或等价数据容器如何在 actor、rollout、reference、reward worker 之间传递，以及 Ray placement、micro-batch、sequence packing 是否改变 tensor shape。任何一个 worker 拿到的字段不一致，importance ratio 都会静默出错。

## DAPO 解决什么

DAPO 是在 GRPO 基础上针对长 CoT RLVR 的工程问题做的改造。DAPO 论文和项目页强调四个技术点：

- Clip-Higher：把 clipping 的上下界解耦，使用更大的上界，缓解低概率探索 token 被过早压制的问题。
- Dynamic Sampling：每个 prompt 的 group 必须同时包含正确和错误样本，避免全对或全错 group 没有有效相对信号。
- Token-Level Policy Gradient Loss：按 token 汇总，而不是先对每条 sequence 平均，减少不同长度 response 带来的偏差。
- Overlong Reward Shaping：对过长或截断 response 做长度惩罚，降低长 CoT 里的 reward 噪声。

DAPO 不是"换个名字的 GRPO"。它更像是把 GRPO 在长推理任务里的几个系统性失败点显式暴露出来：探索不足、无信息 group、长度偏置、过长输出噪声。

## GSPO 解决什么

GSPO，全称 Group Sequence Policy Optimization，核心动机是：GRPO 采用 token-level importance ratio，而长序列和 MoE 场景下 token 级 ratio 可能带来高方差和训练不稳定。

GSPO 的关键改动是把 importance ratio、clipping、rewarding 和 optimization 放到 sequence level。Qwen 团队在 GSPO 论文摘要里明确说它相比 GRPO 提升训练效率和性能，并能稳定 MoE RL training，同时有机会简化 RL infra。

三者的关系大致是：

- GRPO：token-level ratio，细粒度，但长序列和 MoE 下可能不稳定。
- DAPO：保留 GRPO 形态，针对 clipping、采样、token loss、长度惩罚做工程修补。
- GSPO：把关键操作上移到 sequence-level，目标是降低 token-level 噪声，尤其解决 MoE RL 稳定性。

## 熵崩溃

熵崩溃是 policy 的输出分布很快变得过于确定，采样 response 变得高度相似，探索能力下降。DAPO 论文把它描述为训练过程中 policy entropy 快速下降，某些 group 的 responses 几乎相同。

为什么会发生：

- reward 太稀疏或太尖锐，模型只强化少数模式。
- PPO/GRPO clipping 对低概率探索 token 的提升过于保守。
- 训练数据或 verifier 让某些格式模板过度占优。
- KL、entropy bonus、temperature、group size、采样策略设置不当。
- 长 CoT 中，早期 token 一旦收敛到固定模式，后续路径也会被锁死。

对应的优化方向：

- 监控 token entropy、response diversity、unique answer ratio，而不是只看 reward。
- 使用 DAPO 的 Clip-Higher 或类似非对称 clipping。
- 增大/调整采样温度、top-p、group size，但要用 eval 验证。
- 加 entropy regularization，或至少记录 entropy 曲线。
- 使用 dynamic sampling，过滤全对/全错 group。
- 改 reward：不要只奖励格式，不要让短路答案获得高分。

是否需要解决，取决于目标。稳定的确定性分类器并不怕低熵；但长推理、数学、代码、agent 工具调用里，过早低熵通常意味着探索不足，模型会学会单一模板，泛化和自我修正能力变差。

## 熵崩溃和 reward hacking

两者不同，但经常相互强化。

reward hacking 是模型找到奖励函数漏洞，例如只输出固定格式、钻 verifier 解析漏洞、输出超长推理掩盖最终答案。熵崩溃是策略分布收缩，模型不再探索其它行为。

关系是双向的：某种 hack 获得高 reward，RL 会持续提高它的概率；一旦 entropy 降下来，模型就更难跳出这个 hack 模式。反过来，熵崩溃也会让 reward hacking 更难被发现，因为 sampled responses 太相似，eval 看不到失败模式的多样性。

## MoE 训推不一致

MoE 场景下要关注两类不一致：

- 路由不一致：训练时和推理时 expert routing、top-k、负载均衡、随机性、precision 或 kernel 行为不同，会导致 logprobs 和实际 rollout 分布不一致。
- 系统不一致：训练 engine 和 inference engine 分开，权重同步延迟或量化方式不同，导致 behavior policy 与 target policy 偏离。

这会直接影响 GRPO 的 importance ratio。ratio 偏离过大时，梯度方差上升，clip 大量触发，训练信号变差。GSPO 把关键 ratio 上移到 sequence-level，一个重要动机就是让 MoE RL training 更稳定。

R3 通常指 Rollout Routing Replay。它的思路是记录 rollout/inference engine 里的 router 分布或 expert 选择，并在 training forward 里重放这些路由信息，从而对齐训练和推理阶段的 MoE router 行为。这个方向不是在改 reward，而是在修系统分布不一致：同一个 token 如果 rollout 时走了一组 experts，训练算 logprob 时却走了另一组 experts，那么 old logprobs、当前 logprobs 和 importance ratio 的语义都会变差。

MoE RL 稳定性目前可归为三类方向：

- R3：直接对齐 rollout 和 training 的 router 行为，工程上要保存/传递路由信息。
- GSPO：从优化目标层面减少 token-level ratio 对 MoE 和数值差异的敏感性。
- TIS/off-policy correction：从行为策略和目标策略不一致的角度做采样校正或样本过滤。

## 推理优化和 MLA

模型层面的推理优化可以分三类：

- 架构降低 KV cache：MLA、GQA/MQA、滑窗或稀疏 attention。
- MoE 降低 activated parameters：总参数大，但每个 token 只激活部分专家。
- decoding 与服务优化：prefix cache、continuous batching、paged attention、speculative decoding、MTP、多 token prediction、量化、KV cache offload。

MLA，Multi-head Latent Attention，本质是对 K/V 做低秩 latent 压缩，减少 autoregressive decoding 时 KV cache 的存储和带宽压力。DeepSeek-V3 报告明确把 MLA 和 DeepSeekMoE 作为高效推理、低成本训练的核心架构。MLA 可以加快推理，不是因为 attention 复杂度消失，而是因为长上下文生成常被 KV cache memory bandwidth 和显存容量限制；压缩 KV cache 后，batch size、context length 和吞吐空间都更好。

看 MLA 时要记住两点：

- 它主要优化 decoding 阶段的 KV cache，而不是把所有 attention 计算变成常数。
- 它通常要和 kernel、cache layout、并行策略一起看，单独看公式不够。

## DeepSeek-R1-Zero、R1 和 DeepSeek 技术报告

DeepSeek-R1 系列展示的是一种后训练叙事：

- R1-Zero：直接在 DeepSeek-V3-Base 上做大规模 RL，没有先做 SFT。它展示了自验证、反思、长 CoT 等能力，但也有重复、可读性差、语言混杂问题。
- R1：在 RL 前加入 cold-start 数据，再做 reasoning RL；后续还包含 rejection sampling/SFT 和面向人类偏好的 RL 阶段。
- Distill：DeepSeek 用 R1 生成的 reasoning 数据微调 Qwen/Llama 系列小模型，证明蒸馏大模型推理轨迹比直接在小模型上做 RL 更有效。

R1 的底座是 V3-Base，因此 V3 技术报告要一起读。V3 的关键点包括 MoE、MLA、auxiliary-loss-free load balancing、MTP、FP8 mixed precision、DualPipe。

## Qwen3 和 DeepSeek-R1 的差异

不要把 Qwen3 和 DeepSeek-R1 简化成"谁更强"。它们的产品和训练叙事不同。

- DeepSeek-R1：重点是 reasoning model。R1-Zero 验证纯 RL 激发推理，R1 通过 cold-start + 多阶段 RL/SFT 改善可读性和对齐。
- Qwen3：模型族路线，强调 dense/MoE 多尺寸、thinking/non-thinking 模式统一、多语言、agent/coding 能力和 Apache 2.0 开放生态。
- Qwen3-2507：官方模型卡区分 Instruct non-thinking 和 Thinking 模型。非 thinking 版本不输出 `<think></think>`，强调通用、长上下文、工具和对齐能力；Thinking 版本面向更强推理。
- 算法侧：DeepSeek 公开叙事以 GRPO/RLVR 为核心；Qwen 后续提出 GSPO，强调 sequence-level optimization 和 MoE RL 稳定性。

## eval 设计

只看训练 reward 是危险的。一套可用的 eval 迭代标准：

- 任务有效性：指标直接对应用户要解决的问题，而不是只对应 verifier 易判定的子问题。
- 独立性：eval 集不能和训练 prompt、reward rule、rejection sampling 数据同源泄漏。
- 分层：同时看 pass@1、cons@k、format success、tool success、latency、cost、长度、重复率、拒答和安全边界。
- 回归：每次训练不仅看目标任务提升，也看通用能力、短答质量、中文/英文、代码、工具调用是否退化。
- 可诊断：失败样例要能归因到 reward、数据、采样、模板、长度截断、推理参数或系统 bug。
- 时效性：业务需求变化时，eval 权重和样例要跟着改，不能用旧 benchmark 证明新需求。

有说服力的项目叙述，不是"AIME 提升了多少"，而是"reward 上升时 held-out verifier 和人工 trace 出现退化，所以把 reward/eval 解耦并新增了长度、重复率和工具调用成功率指标"。

## 参考资料

- [DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models](https://arxiv.org/abs/2402.03300)：GRPO 原始论文，解释 critic-free、group reward baseline 和公式。
- [DeepSeek-R1 GitHub](https://github.com/deepseek-ai/DeepSeek-R1)：R1-Zero、R1 pipeline、distillation、benchmark 和使用建议。
- [DeepSeek-V3 Technical Report](https://arxiv.org/abs/2412.19437)：MLA、DeepSeekMoE、auxiliary-loss-free load balancing、MTP、FP8、DualPipe。
- [DAPO paper](https://dapo-sia.github.io/static/pdf/dapo_paper.pdf)：Clip-Higher、Dynamic Sampling、Token-Level Loss、Overlong Reward Shaping。
- [GSPO paper](https://arxiv.org/abs/2507.18071)：sequence-level importance ratio、sequence-level clipping、MoE RL 稳定性。
- [Stabilizing MoE Reinforcement Learning by Aligning Training and Inference Routers](https://arxiv.org/abs/2510.11370)：R3 / Rollout Routing Replay，用于缓解 MoE RL 训练和推理 router 不一致。
- [verl GitHub](https://github.com/verl-project/verl) 和 [verl 参数文档](https://verl.readthedocs.io/en/latest/ascend_tutorial/dev_guide/model_dev/parameter_and_metrics.html)：RL post-training 框架、remove padding、Ulysses sequence parallel。
- [TRL GRPOTrainer](https://huggingface.co/docs/trl/grpo_trainer)：GRPO 工程配置、vLLM/off-policy mismatch、importance sampling 参数。
- [OpenRLHF RL Training Guide](https://openrlhf.readthedocs.io/en/latest/agent_training.html)：PPO/GRPO/RLOO、异步 rollout、off-policy correction、entropy、dynamic sampling。
- [NeMo RL GRPO Guide](https://docs.nvidia.com/nemo/rl/nightly/guides/grpo.html)：GRPO walkthrough 和 importance sampling correction。
- [Qwen3 Technical Report](https://arxiv.org/abs/2505.09388) 和 [Qwen3-235B-A22B-Instruct-2507 model card](https://huggingface.co/Qwen/Qwen3-235B-A22B-Instruct-2507)：thinking/non-thinking、多语言、MoE、长上下文和 2507 模型定位。
- [[GRPO]]
