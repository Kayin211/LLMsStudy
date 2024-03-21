# Intent-based Prompt Calibration: Enhancing prompt optimization with synthetic boundary cases

论文链接：https://arxiv.org/abs/2402.03099

github：https://github.com/eladlev/autoprompt

## 1.论文背景

大模型对 prompt 高度敏感，即使提示格式稍有修改也会显着影响模型的性能。最近的研究表明，可以通过大模型本身来优化 prompt，每个 prompt 都会根据给定的基准指标分配一个分数。优化过程中，首先通过提供一个元提示（meta-prompt）来迭代执行，这个元提示结合最近几次的提示分数，最后引导模型选出分数更高的 prompt。评估此类方法需要大量的高质量基准数据，然而此类基准数据并不常见。虽然 LLM 能够细化用户提供的 prompt，解决初始 prompt 的歧义性。然而，在没有额外信息的情况下，模型必须猜测用户的真实意图，这在许多情况下可能导致不准确的结果。


## 2.论文解决了什么问题

有趣的是，LLM 已被证明在生成高质量和丰富的数据集方面非常有效，这些数据集能够提升模型在多样化任务上的性能。本文提出基于意图的提示校准（IPC，Intent-based Prompt Calibration）系统，该系统旨在通过合成示例根据用户的意图校准 prompt。校准过程通过迭代构建具有挑战性边缘案例的样本数据集，并根据生成的基准来优化 prompt。


## 3.论文方法

IPC整体系统架构如图，该系统主要由 Dataset、Estimator、Evaluator、Optimizer 四部分组成。

![]()

Dataset 负责管理数据集，执行数据的插入、修改、删除和应用函数等操作，并进行数据清洗以去除语义重复和进行语义抽样。
Estimator 负责估计一批样本，它实现了两种估计器：人类注释和大型语言模型（LLM）估计。支持 Argilla UI 进行人类注释，以及使用 Langchain 集成的LLM。
Evaluator 负责在预测和注释阶段之后评估记录，该组件接受一个函数并将其应用于每一行数据。它还负责定义错误并使用分析器进行错误分析。
Optimizer 负责管理整个优化过程，执行迭代步骤，并负责停止优化过程并返回最终校准的 prompt。

IPC系统具体实现流程图：

![]()

作者从初始的 prompt 和任务描述 description 开始，用户还可以在少量样本的设置中提供一些示例，然后，在校准优化过程中，系统会迭代执行以下步骤：

1.为任务和当前 prompt 提出一些具有挑战性和多样性的样本。
2.在生成的数据集上评估当前 prompt，并进行分析。
3.根据最近几次的 prompt，生成一个分数更高的 prompt。当最近几步没有改进，或达到最大迭代次数，或达到预算上限，优化过程就会结束。

本文 IPC 系统的基线配置针对分类任务进行了优化，将准确度被设定为评分函数，并通过混淆矩阵和提示错误分类进行错误分析。

![]()

在每次迭代中，根据当前提示生成新的样本，利用这些样本的误分类来细化提示，直到它能够校准到用户的意图。


## 4.实验分析

作者使用 IMDB 评论数据集作为实验的基础数据，评估三个二元分类任务的即时校准过程：(1) Spoiler detection, (2) Sentiment analysis, and (3) Parental Guidance (PG) detection.

![]()

## 5.其他

Automatic Prompt Engineering 的三种方法：

APE：根据任务数据集优化给定 prompt 模板，四个步骤：

  1.使用 LLM 生成候选 prompt 集合
  2.调用 LLM 评估各个候选 prompt 质量，打分函数：1）Execution accuracy 在训练集上执行 prompt 后，得到的任务 metric（如Accuracy、F1等）。 2）Log probability log P(A|[p;Q])，不使用任务 metric，而是评估生成 desired answer 的概率。 3）Efficient score estimation 从训练集中抽取少部分数据用于打分筛选 prompt。
  3.生成高分 prompt 语义相近的 prompt：迭代蒙特卡洛搜索采样 Monte Carlo Search
  4.返回最高评分的 prompt

APO：在文本空间实现 gradient descent 过程，本质是构建一个optimizer，其框架是参照 gradient decent 来设计，三个步骤：
  
  1.得到当前 prompt 的 gradient：给定一批 error samples（当前 prompt 无法预测正确的），让 LLM 给出当前 prompt 预测错误的原因，即文本形式的 gradient。
  2.应用 gradient，得到 new prompt：1）使用 LLM 来 edit 原来的 prompt，目标是修复 gradient。 2）和 APE 一样，进行 resample，扩充相似语义的 prompt。
  3.挑选出好的prompt，进入下一轮迭代：APO在每轮迭代中，最外层包含一个beam search过程，以便强化探索。

OPRO：让 LLM 基于过往的迭代记录、优化目标，自己总结规律，逐步迭代 prompt，整个过程在文本空间上完成，四个步骤：
  
  1.使用 meta-prompt，让 LLM 成为 Optimizer LLM。meta-prompt包含两个核心部分：1）solution-score pairs，即以往的迭代路径，包括 solution（即prompt） + 分数（任务表现），实践中按照分数大小，从低到高排列top20的结果。2）task description，包含一些任务的examples、优化的目标等。
  2.基于对过往迭代规律的理解，Optimizer LLM 生成新的 solution。即将 meta-prompt 给 Optimizer LLM，生成的内容即为新的 solution。在实践中，为了提升优化的稳定性，这一步重复了8次。
  3.在 Scorer LLM 上应用 prompt（即新的 solution），评估效果并记录到 meta-prompt 中，然后继续下一轮迭代。这里的 Scorer LLM 是实际使用 prompt 的LLM，与 Optimizer LLM 可以是不同的。
  4.当效果无法再提升、或者到达预先给定的 step 上限，整个迭代过程停止。返回得分最高的 prompt 作为优化结果。

IPC 亮点：构建一组挑战性边缘案例的数据集，并据此优化提示词

## 6.总结

理论上，IPC 对各类任务（分类、生成等）是通用的，只需定义好评价指标。只要场景里使用了 Prompt，则可以考虑 IPC，在 benchmark 上提分、优化 LLM 标注器的效果、根据用户反馈优化Prompt 等似乎有可行性。
