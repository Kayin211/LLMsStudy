# Rephrase and Respond Let Large Language Models Ask Better Questions for Themselves

论文链接：https://arxiv.org/abs/2311.04205

github：https://uclaml.github.io/Rephrase-and-Respond

## 1.论文背景

误解不仅在人际交流中发生，还在人类与大型语言模型 LLM 之间发生。这种差异可能导致 LLMs 以意外的方式解释看似明确的问题，从而产生不正确的响应。尽管广泛认为 prompt 的质量，比如一个问题，显著影响 LLMs 响应的质量，但仍然缺乏一种系统的方法来制定 LLMs 更好理解的问题。

![](ambiguities in the question for LLMs)

## 2.论文解决了什么问题

人类的思维和逻辑思维有着很大的差异，这影响了大语言模型的性能。经过重述的问题往往会增强语义的清晰度，解决了模糊性的问题。新生成的问题更加详细，并且具有更清晰的问题格式。因此，作者提出了一种名为 ‘Rephrase and Respond’（RaR）的方法。它提示 LLM 重述给定的问题，并对问题做出响应。这个方法提高了 LLM 回答问题的质量。

![](original and self-rephrase)

## 3.论文方法

作者提出了两个方法：One-step RaR 和 Two-step RaR，通过让 LLM 自己重新表述问题来促进它们更好的回答。

One-step RaR：LLM 被要求重述问题并使用单个查询来回答问题

![](RaR)

Two-step RaR：首先，针对给定的问题，使用一个专门的 Rephrasing LLM 生成一个复述问题；其次，将原始问题和复述后的问题结合起来，用于提示一个 Responding LLM 进行回答。

![](Two-step RaR)

重述问题的 LLM 和回答的 LLM 可以是相同或不同的模型。因此，较强的 LLM，如 GPT-4 重新表述的问题可以帮助像 Vicuna 这样较弱的 LLM 产生更准确的回答。与One-step RaR 的 prompt 相比，在 Two-step RaR 中，研究人员通过保留用户的问题来保持原始上下文，同时添加 LLM 重新表述的问题以帮助 LLM 更好地理解。这防止了重述问题与原始问题的可能差异。

## 4.实验分析

1.作者在 10 个任务上进行了实验，发现无论是 (One-step) RaR 还是 Two-step RaR，都在提高 GPT4 的回答准确率方面显示出了一致的有效性。值得注意的是，在原本对于 GPT-4 极具挑战性的任务上，RaR 展现出了显著的改进效果，甚至在某些情况下准确率接近 100%。

![](RaR tasks)
![](RaR result)

2.作者采用了 Two-step RaR 来探究 GPT-4、GPT-3.5 和 Vicuna-13b-v.15 等不同模型的表现。实验结果显示，对于那些具备更复杂架构和更强大处理能力的模型，如 GPT-4，RaR 方法能够显著提升其处理问题的准确性和效率。而对于较为简单的模型，例如 Vicuna，尽管改进幅度较小，但依然证明了 RaR 策略的有效性。不同等级的语言模型复述的问题在质量和效果上存在差异。

![](RaR on 3 models)

3.与思维链（CoT）的区别：作者阐明了 RaR 在数学上与 CoT 的不同之处，以及它们如何可以轻松结合。作者发现，通过使用 RaR，GPT-4 能够纠正给定示例中的逻辑缺陷，从而提高 few-shot CoT 的质量和稳健性。

![](mathematical formulations of RaR and CoT)
![](coin flip question)
![](RaR + Few-shot CoT)

## 5.其他

post-hoc prompting 以及 reflexion 和 self-refine 等反思策略，是让 LLM 修改自己的响应，这些响应来自各种外部反馈源或批评模型，而有研究表明 LLM 的自校正能力存在潜在的限制，这表明 LLM 可能无法自我纠正他们的推理过程。与允许 LLM 自我完善其响应相反，RaR 是让 LLM 重新表述最初由人类制作的问题。

## 6.总结

RaR 在实验中确实展现出了显著的改进效果，甚至在某些情况下准确率接近 100%，这也说明利用 RaR 或许能够缩小人类思维和大模型思维的差距，以此为前提与其他改进 LLM 的方法相结合，比如：CoT，reflexion等，或许能进一步改善模型效果。