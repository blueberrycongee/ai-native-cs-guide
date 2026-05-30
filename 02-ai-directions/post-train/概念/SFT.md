# SFT

SFT，全称 Supervised Fine-Tuning，是用示范数据继续训练模型，让它学会按特定任务和格式回答。

它是 post-training 的第一站。很多模型能变成“会聊天、会按指令做事”，都离不开类似的监督微调阶段。

## 工作机制

SFT 数据通常是 prompt 和理想回答：

```json
{
  "messages": [
    {"role": "user", "content": "把这段日志总结成三条问题"},
    {"role": "assistant", "content": "1. ..."}
  ]
}
```

训练时，模型仍然做 next-token prediction，只是数据换成了任务示范。它学的是在某种上下文和格式下如何继续生成。

## 工程形态

SFT 项目要处理：

- 数据格式和 chat template。
- 训练集、验证集和 eval 集。
- LoRA/QLoRA 或全量微调。
- checkpoint、日志和样例输出。
- 微调前后对比。

最小项目不要追求数据大。先准备 100 到 1000 条高质量任务样例，比抓一堆脏数据更有意义。

## 常见失败模式

- 数据格式和推理时 prompt 格式不一致。
- 训练样例太少，却期待模型获得新知识。
- 只看 loss，不看生成质量。
- eval 集和训练集泄漏。
- 模型学会表面格式，但事实错误更多。

## 参考工具

- [TRL SFTTrainer](https://huggingface.co/docs/trl)
- [Axolotl](https://docs.axolotl.ai/)
- [[Evals]]
- [[Post-Train 路径]]
