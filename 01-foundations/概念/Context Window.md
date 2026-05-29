# Context Window

Context window 是模型一次能看到的上下文范围，通常按 [[Token]] 计算。

它不是模型的长期记忆。一次请求里没放进去的信息，模型就不能直接使用。放进去的信息太多，也不代表模型一定会稳定用好。

相关概念：

- [[Token]]
- [[Attention]]
- [[Context Engineering]]
- [[RAG]]
- [[Prompt Cache]]

## 学到什么程度

入门阶段需要知道：

- 上下文窗口限制了一次请求能放多少输入和输出
- 长上下文通常意味着更高成本和更高延迟
- 重要信息要放在模型容易使用的位置
- 长上下文不能替代检索、摘要和任务拆分

不必一开始研究所有长上下文论文。先在项目里感受 token 预算、延迟和答案质量之间的关系。

## 在项目里怎么出现

你会在这些地方遇到 context window：

- 把整份 PDF 塞进 prompt 后，成本和延迟都变高
- [[Agent]] 工具调用多轮循环后，历史消息越来越长
- [[RAG]] 检索返回太多内容，模型回答反而变差
- 对话产品需要决定保留哪些历史、压缩哪些历史、丢弃哪些历史

所以 context window 的核心问题不是“越长越好”，而是“哪些信息值得占用上下文”。

## 资料

- [OpenAI Prompt Caching](https://platform.openai.com/docs/guides/prompt-caching)：理解重复上下文如何影响成本和延迟。
- [Anthropic Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)：看显式 cache breakpoint 的设计。
