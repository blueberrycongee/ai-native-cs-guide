# Dataset Pipeline

Dataset Pipeline 是把原始资料变成可训练 token 序列的过程。预训练不是把文本文件扔给模型这么简单。数据清洗、去重、切分、混合比例、采样方式都会影响模型行为。

## 工作机制

典型流程：

```text
raw documents
  -> filter and clean
  -> deduplicate
  -> tokenize
  -> pack into fixed-length sequences
  -> shuffle and batch
```

如果数据里有重复、垃圾文本、泄露评测集或格式污染，模型会学到这些东西。小实验里也一样，只是影响更容易被忽略。

## 工程形态

训练数据通常要记录：

- 数据来源和许可。
- 清洗规则。
- tokenizer 版本。
- train/validation split。
- 文档到 token 的转换脚本。
- 数据版本 hash。

没有这些记录，实验很难复现。

## 最小例子

做小模型实验时，至少保存：

```text
data/raw/
data/processed/
tokenizer.json
prepare_dataset.py
dataset_config.md
```

这样实验者能解释同一次训练使用了什么数据，而不是只说"我拿了一些文本"。

## 参考资料

- [The Pile paper](https://arxiv.org/abs/2101.00027)
- [Hugging Face Datasets](https://huggingface.co/docs/datasets)
- [[Tokenizer Training]]
