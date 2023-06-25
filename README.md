# 《Prompt Engineering Guide》读书笔记

## 基本信息

《Prompt Engineering Guide》的GitHub仓库：  
https://github.com/dair-ai/Prompt-Engineering-Guide

我写读书笔记时主要看一下两个网页的内容，互相对比，加深理解。
* 英文版链接：https://www.promptingguide.ai
* 中文版链接：https://www.promptingguide.ai/zh

## 各种疑问

这里会总结阅读各个章节时遇到的疑问。

### LLM Settings / 模型设置

关于`Temperature`参数的解释，中文版中有如下描述：
> 如果调高该参数值，大语言模型可能会返回更随机的结果，也就是说这可能会带来更多样化或更具创造性的产出。我们目前也在增加其他可能 token 的权重。

而英文版的描述是：
> Increasing temperature could lead to more randomness, which encourages more diverse or creative outputs. You are essentially increasing the weights of the other possible tokens.

重点在于这一句话的意思不同：
```text
EN: You are essentially increasing the weights of the other possible tokens.

ZH: 我们目前也在增加其他可能 token 的权重。
```

我觉得这里应该采用英文版的说法。这句话的意思是，当你调高了`temperature`参数，基本上等于调高了其他可能的 token 的权重。

#### 背景知识 - token

在这段描述中，出现了 token 这个概念。以下是关于 token 概念的背景知识（通过 ChatGPT 生成）

```md
Q: 在研究像ChatGPT这样的大语言模型中，经常会提到token这个词，我想知道这个词代表的概念，你可以给我推荐3篇相关的文章吗？最好是简单易懂的文章，我目前正在学习大语言模型，对于AI没有什么基础。

A: 当讨论到像ChatGPT这样的大语言模型时，"token"一词通常指文本数据中最小的可分离单元，例如单词、标点符号或者特殊字符等。以下是三篇简单易懂的文章，可供您了解更多关于token的概念和在大语言模型中的应用：
1. "What are Tokens in Natural Language Processing?" by George Seif. 这篇文章解释了什么是token，以及在自然语言处理中的应用。链接：https://towardsdatascience.com/what-are-tokens-in-natural-language-processing-13e5312cbf56
2. "Tokenization explained" by Rhea Moutafis. 这篇文章介绍了tokenization的概念和过程，并提供了一些实际应用的例子。链接：https://towardsdatascience.com/tokenization-explained-8d0f8441e83e
3. "A Beginner's Guide to Tokenization" by Mariya Yao. 这篇文章介绍了什么是tokenization，以及在自然语言处理中的重要性和应用。链接：https://heartbeat.fritz.ai/a-beginners-guide-to-tokenization-46c4f512d4e5
```

```md
Q: 你可以帮我总结一下这篇文章的要点吗？  
"What are Tokens in Natural Language Processing?" by George Seif.
链接：https://towardsdatascience.com/what-are-tokens-in-natural-language-processing-13e5312cbf56

A: 这篇文章解释了自然语言处理中的“tokens”是什么，以及它们在文本数据处理中的作用。要点如下：
1. Token是文本数据中的最小可分离单元，例如单词、标点符号或者特殊字符等。
2. Tokenization是将一段文本数据分割为单独的tokens的过程，可以通过空格、标点符号或其他规则来分割。
3. Tokenization是自然语言处理中的重要步骤，因为它是将自然语言转换为计算机可以处理的离散数据的关键步骤。
4. Tokenization可以用于处理各种自然语言处理任务，例如文本分类、情感分析、机器翻译等。
5. Tokenization可能存在一些挑战，例如处理多语言和异体字等问题。
总的来说，该文章简要介绍了“tokens”的概念和在自然语言处理中的应用，对于初学者来说是一个很好的入门材料。
```

```md
Q: 请问大语言模型是如何计算每个token的权重的？

A: 大语言模型计算每个token的权重的过程是基于概率模型的。在训练模型时，模型会学习一个概率分布，用于计算给定上下文条件下每个可能的token出现的概率。具体来说，模型会根据文本语料库中的数据，学习一个语言模型，即根据前面出现的词或token预测下一个词或token的概率。这个概率通常使用条件概率来表示，即P(token|context)，其中context是前面的词或token。  
在测试或生成阶段，模型会根据输入的上下文计算每个可能的token的条件概率，并选择具有最高概率的token作为下一个输出。这个过程通常被称为贪心搜索。此外，大语言模型还可以使用一些改进的搜索算法，例如束搜索（beam search）和采样搜索（sampling），以产生更加多样化和创造性的输出。  
需要注意的是，对于一些新出现的或者罕见的token，模型可能会出现概率估计不准确的情况，因此在实际应用中，需要对模型进行一些调优和优化，以提高模型的性能和鲁棒性。
```

#### 背景知识 - nucleus sampling

TODO

### Basics of Propmting / 基础概念

提示工程（Prompt Engineering）就是探讨如何设计出最佳提示词，用于指导语言模型帮助我们高效完成某项任务。  
This approach of designing optimal prompts to instruct the model to perform a task is what's referred to as prompt engineering.

零样本提示（zero-shot prompting），即用户不提供任务结果相关的示范，直接提示语言模型给出任务相关的回答。  
It's referred to as zero-shot prompting, i.e., you are directly prompting the model for a response without any examples or demonstrations about the task you want it to achieve.

小样本提示（Few-shot Prompting），即用户提供少量的提示范例，如任务说明等。  
It is referred to as few-shot prompting where you provide exemplars (i.e., demonstrations).

### Elements of a Prompt / 提示词要素

提示词可以包含以下任意要素：  
* 指令：想要模型执行的特定任务或指令。  
* 上下文：包含外部信息或额外的上下文信息，引导语言模型更好地响应。  
* 输入数据：用户输入的内容或问题。  
* 输出指示：指定输出的类型或格式。  

### General Tips for Designing Prompts / 设计提示的通用技巧

1. 从简单开始
1. 指令
1. 具体性
1. 避免不精确
1. 做还是不做？

:warning: 在这一章发现中文版中有几个小错误，后续章节应该以英文版为主，辅助阅读中文版加深理解。

## 其他

1. https://github.com/f/awesome-chatgpt-prompts
1. https://github.com/PlexPt/awesome-chatgpt-prompts-zh