# EmotionPrompt: Leveraging Psychology for Large Language Models Enhancement via Emotional Stimulus

论文链接: https://arxiv.org/pdf/2307.11760.pdf

## 1.论文背景

LLM 在各个领域取得了显著的性能，但 LLM 对 prompt 的敏感性仍然是其应用受限的原因。之前的方法主要集中在改进模型的鲁棒性，对于人机交互方面的研究较少。EmotionPrompt 方法通过引入心理学知识，将情感刺激融入 prompt 中，从而提升 LLM 的性能。

## 2.论文解决什么问题

本文提出 EmotionPrompt 方法，通过将情感刺激融入原始提示语，利用心理学理论来增强 LLM 的性能，实验证明 EmotionPrompt 在多个任务上都能显著提升模型的准确性、真实性和信息量。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/EmotionPrompt.png)

## 3.论文方法

1） 基于心理学理论，将 prompt 归为2个大类，一个 social effect(社会影响)、另一个是 self-esteem(自尊心)，设计了11个情感刺激，用于增强 LLM 性能。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Emotional%20stimulus.png)
![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/11%E4%B8%AA%20stimulus.png)

2） 通过可视化情感刺激对最终输出的输入注意力贡献，解释了 EmotionPrompt 的工作原理。 

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Effectiveness%20of%20Emotional%20Prompt.png)
 
3） 分析了情感刺激对原始提示语表示的增强效果，发现情感刺激可以增强原始提示语的注意力。

4） 发现积极的词语对结果的贡献更大，对设计的情感刺激中的积极词语进行了总结，并计算了它们对八个任务的总贡献。

## 4.实验分析

本文在八个指令归纳任务上评估了 EmotionPrompt 的零样本学习和少样本学习。这些任务涵盖了语言理解的不同方面，从简单的短语结构到相似性和因果关系的识别。为了评估 EmotionPrompt 的泛化性能，作者在四个 LLM 模型上进行了评估：ChatGPT、Vicuna-13b、Bloom和Flan-T5-Large。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Emotional%20Prompt%20result.png)

实验结果表明，EmotionPrompt 在八个任务上（包括句子相似度、词义相同性判断和因果关系判断等）的零样本和少样本设置下，使用相同的单一提示模板，显著优于原始提示和 Zero-shot-CoT。施加了 EmotionPrompt 对各个模型在各个方面都提升了模型的性能。

作者指出：

1.施加了 EmotionPrompt 在所有测试任务上均有提升，相较于其他 Prompt 工程的复杂性，EmotionPrompt 作为一个挂件确实简单又好用；
2.在 Few-shot 设置中，EmotionPrompt 带来了更大的提升，相比 Zero-shot，EmotionPrompt 更加适用于 Few-shot；
3.在不同困难度的任务中，EmotionPrompt 都表现出了几乎一致的有效性；
4.在具有超越 CoT 的性能的同时，EmotionPrompt 又具有极高的可扩展性，可以插入到 APE（ Automatic Prompt Engineer）之中实现无缝对接。

## 5.总结

本文提出的 EmotionPrompt 方总体上在不同任务不同模型中都表现出良好性能，而且实现过程也不复杂，好像仅仅通过在原始 prompt 后增加一些积极情绪刺激就可提升实验效果，这种方式挺简单粗暴的，如果能提升实验效果何乐而不为呢？