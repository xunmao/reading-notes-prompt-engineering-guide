# 《Prompt Engineering Guide》读书笔记

## 之后的计划

1. ChatGPT 学习导航：https://learningprompt.wiki/docs/chatgpt-learning-path
1. Open AI Prompt engineering：https://help.openai.com/en/collections/3675942-prompt-engineering
    1. Best practices for prompt engineering with OpenAI API：https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api
1. 了解 https://huggingface.co/

## 基本信息

《Prompt Engineering Guide》的GitHub仓库：  
https://github.com/dair-ai/Prompt-Engineering-Guide

我写读书笔记时主要看以下两个网页的内容，互相对比，加深理解。
* 英文版链接：https://www.promptingguide.ai
* 中文版链接：https://www.promptingguide.ai/zh

## Introduction / 提示工程简介

### LLM Settings / 模型设置

关于`Temperature`参数的解释，中文版中有如下描述：
> 如果调高该参数值，大语言模型可能会返回更随机的结果，也就是说这可能会带来更多样化或更具创造性的产出。我们目前也在增加其他可能 token 的权重。

而英文版的描述是：
> Increasing temperature could lead to more randomness, which encourages more diverse or creative outputs. You are essentially increasing the weights of the other possible tokens.

重点在于这一句话的意思不同：
> EN: You are essentially increasing the weights of the other possible tokens.  
> ZH: 我们目前也在增加其他可能 token 的权重。

我觉得这里应该采用英文版的说法。这句话的意思是，当你调高了`temperature`参数，基本上等于调高了其他可能的 token 的权重。

#### 背景知识 - token

在这段描述中，出现了 token 这个概念。以下是关于 token 概念的背景知识（通过 ChatGPT 生成）

_Prompt:_
> 在研究像ChatGPT这样的大语言模型中，经常会提到token这个词，我想知道这个词代表的概念，你可以给我推荐3篇相关的文章吗？最好是简单易懂的文章，我目前正在学习大语言模型，对于AI没有什么基础。

_Output:_
> 当讨论到像ChatGPT这样的大语言模型时，"token"一词通常指文本数据中最小的可分离单元，例如单词、标点符号或者特殊字符等。以下是三篇简单易懂的文章，可供您了解更多关于token的概念和在大语言模型中的应用：
> 1. "What are Tokens in Natural Language Processing?" by George Seif. 这篇文章解释了什么是token，以及在自然语言处理中的应用。链接：https://towardsdatascience.com/what-are-tokens-in-natural-language-processing-13e5312cbf56
> 2. "Tokenization explained" by Rhea Moutafis. 这篇文章介绍了tokenization的概念和过程，并提供了一些实际应用的例子。链接：https://towardsdatascience.com/tokenization-explained-8d0f8441e83e
> 3. "A Beginner's Guide to Tokenization" by Mariya Yao. 这篇文章介绍了什么是tokenization，以及在自然语言处理中的重要性和应用。链接：https://heartbeat.fritz.ai/a-beginners-guide-to-tokenization-46c4f512d4e5

_Prompt:_
> 你可以帮我总结一下这篇文章的要点吗？  
> "What are Tokens in Natural Language Processing?" by George Seif. 链接：https://towardsdatascience.com/what-are-tokens-in-natural-language-processing-13e5312cbf56

_Output:_
> 这篇文章解释了自然语言处理中的“tokens”是什么，以及它们在文本数据处理中的作用。要点如下：
> 1. Token是文本数据中的最小可分离单元，例如单词、标点符号或者特殊字符等。
> 2. Tokenization是将一段文本数据分割为单独的tokens的过程，可以通过空格、标点符号或其他规则来分割。
> 3. Tokenization是自然语言处理中的重要步骤，因为它是将自然语言转换为计算机可以处理的离散数据的关键步骤。
> 4. Tokenization可以用于处理各种自然语言处理任务，例如文本分类、情感分析、机器翻译等。
> 5. Tokenization可能存在一些挑战，例如处理多语言和异体字等问题。
> 总的来说，该文章简要介绍了“tokens”的概念和在自然语言处理中的应用，对于初学者来说是一个很好的入门材料。

_Prompt:_
> 请问大语言模型是如何计算每个token的权重的？

_Output:_
> 大语言模型计算每个token的权重的过程是基于概率模型的。在训练模型时，模型会学习一个概率分布，用于计算给定上下文条件下每个可能的token出现的概率。具体来说，模型会根据文本语料库中的数据，学习一个语言模型，即根据前面出现的词或token预测下一个词或token的概率。这个概率通常使用条件概率来表示，即P(token|context)，其中context是前面的词或token。  
> 在测试或生成阶段，模型会根据输入的上下文计算每个可能的token的条件概率，并选择具有最高概率的token作为下一个输出。这个过程通常被称为贪心搜索。此外，大语言模型还可以使用一些改进的搜索算法，例如束搜索（beam search）和采样搜索（sampling），以产生更加多样化和创造性的输出。  
> 需要注意的是，对于一些新出现的或者罕见的token，模型可能会出现概率估计不准确的情况，因此在实际应用中，需要对模型进行一些调优和优化，以提高模型的性能和鲁棒性。

#### 背景知识 - nucleus sampling

TODO

### Basics of Propmting / 基础概念

提示工程（Prompt Engineering）就是探讨如何设计出最佳提示词，用于指导语言模型帮助我们高效完成某项任务。  
This approach of designing optimal prompts to instruct the model to perform a task is what's referred to as prompt engineering.

零样本提示（Zero-shot Prompting），即用户不提供任务结果相关的示范，直接提示语言模型给出任务相关的回答。  
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

### Examples of Prompts / 提示词示例

Text Summarization / 文本概括

_Prompt:_
> Explain the paper "A Beginner's Guide to Tokenization" by Mariya Yao in 3-5 sentences.  
> A:

_Output:_
> "A Beginner's Guide to Tokenization" by Mariya Yao is a comprehensive introduction to the concept of tokenization in natural language processing. The paper explains how tokenization works, the different approaches to tokenization, and the challenges involved in tokenizing text. It also provides practical examples of tokenization using Python and NLTK, and discusses best practices for tokenization in different scenarios. Overall, the paper is a valuable resource for anyone who wants to understand the basics of tokenization and its applications in NLP.

_Prompt:_
> Explain how tokenization works according to the paper "A Beginner's Guide to Tokenization" by Mariya Yao in 3-5 sentences.  
> A:

_Output:_
> According to the paper "A Beginner's Guide to Tokenization" by Mariya Yao, tokenization is the process of breaking down text into smaller units called tokens. These tokens can be words, phrases, or even individual characters, depending on the specific application. Tokenization typically involves removing punctuation, converting all text to lowercase, and handling special cases such as contractions and hyphenated words. The resulting tokens can then be used as input for further natural language processing tasks, such as sentiment analysis or machine translation.

_Prompt:_
> Explain the challenges involved in tokenizing text according to the paper "A Beginner's Guide to Tokenization" by Mariya Yao in 3-5 sentences in Chinese.  
> A:

_Output:_
> 根据Mariya Yao的论文《初学者的分词指南》，文本分词面临着许多挑战。其中的挑战包括处理缩写词、连字符、网址和特殊字符等。此外，对于某些语言和文本类型，分词可能会更加复杂，因为它们具有不同的语法结构和规则。因此，在进行文本分词时需要认真考虑这些挑战，以确保分词的准确性和一致性。

Information Extraction / 信息提取

TODO

Question Answering / 问答

_Prompt:_
> Answer the question based on the paper "A Beginner's Guide to Tokenization" by Mariya Yao. List up the answer in 3-5 points in Chinese.  
> Q: What are the different approaches to tokenization?  
> A:

_Output:_
> 根据Mariya Yao的论文《初学者的分词指南》，文本分词有以下不同的方法：
> 
> 1. 基于规则的分词方法，使用预定义的规则来识别单词和短语。
> 2. 基于机器学习的分词方法，使用机器学习算法来自动学习文本中的单词和短语。
> 3. 混合方法，结合使用规则和机器学习算法来进行分词。
> 
> 这些方法各有优缺点，具体使用哪种方法取决于文本的性质、应用场景以及分词的准确性需求。

Text Classification / 文本分类

TODO

Conversation / 对话

角色提示（Role Prompting）  
Note how you are explicitly telling a LLM how to behave through the instruction. This is sometimes referred to as role prompting.

Code Generation / 代码生成

_Prompt:_
> /*  
> Ask the user for their name and say "Hello".  
> Provide a form to the user for inputing their name.  
> */

_Output:_
> Here's an example code snippet in HTML and JavaScript that asks the user for their name and says "Hello" using a form:
> 
> [the generated source code ......]
> 
> This code defines an HTML form with an input field for the user's name and a submit button. When the user submits the form, a JavaScript function is called that retrieves the user's name from the input field and displays a greeting using an alert dialog box. The form's default submission behavior is prevented using the `event.preventDefault()` method, which allows the JavaScript code to handle the form submission instead.

See the generated source code file [hello-form](./hello-form.html).

_Prompt:_
> """  
> Create a MySQL query based on the info below.
> 
> Info: Table Product
> columns = [product_id, product_name, product_type, smale_mprioce, purchase_price]
> primary key = [product_id]
> 
> Format: The product_type should be returned in the following format:
> A: Clothes
> B: Office Supplies
> C: Kitchen Items
> Clothes, Office Supplies and Kitchen Items are valid product_type values.
> The "A: ", "B: " and "C: " are added for formatting the output.
> 
> Question: How can I query the product name and the formatted product type?
> 
> Answer:  
> """

_Output:_
> Here's a MySQL query to retrieve the product name and formatted product type as specified:
> 
> [the generated source code ......]
> 
> This query uses a `CASE` statement to conditionally add the appropriate prefix for each product type, and concatenates it with the product type using the `CONCAT` function. The resulting output will have two columns: `product_name` and `formatted_type`, with each row displaying the product name and its corresponding formatted product type.

这是ChatGPT生成的代码：
```SQL
SELECT 
  product_name,
  CONCAT(
    CASE product_type 
      WHEN 'Clothes' THEN 'A: '
      WHEN 'Office Supplies' THEN 'B: '
      WHEN 'Kitchen Items' THEN 'C: '
    END,
    product_type
  ) AS formatted_type 
FROM 
  Product;
```

这是《SQL基础教程（第2版）》中的示例代码：
```SQL
SELECT product_name,
    CASE WHEN product_type = 'Clothes'
         THEN 'A :' || product_type
         WHEN product_type = 'Office Supplies'
         THEN 'B :' || product_type
         WHEN product_type = 'Kitchen Items'
         THEN 'C :' || product_type
         ELSE NULL
    END AS formatted_type
  FROM Product;
```

由于在指令没有给出更加详细的需求，ChatGPT生成的代码中没有考虑到除了`Clothes`、`Office Supplies`和`Kitchen Items`之外的情况。

Reasoning / 推理

TODO

## Prompting Techniques / 提示技术

### Zero-Shot Prompting / 零样本提示

Instruction tuning is essentially the concept of finetuning models on datasets described via instructions.  
指令调整本质上是在通过指令描述的数据集上微调模型的概念。

1. "FINETUNED LANGUAGE MODELS ARE ZERO-SHOT LEARNERS" by Jason Wei et al.
1. "Deep reinforcement learning from human preferences" by Paul Christiano et al.

### Few-Shot Prompting / 小样本提示

这句话不太理解
> EN: The demonstrations serve as conditioning for subsequent examples where we would like the model to generate a response.  
> ZH: 演示作为后续示例的条件，我们希望模型生成响应。

这篇论文中提到的3点建议不太理解
> "Rethinking the Role of Demonstrations: What Makes In-Context Learning Work?" by Sewon Min.
> 
> Tips:
> * "the label space and the distribution of the input text specified by the demonstrations are both important (regardless of whether the labels are correct for individual inputs)"
> * the format you use also plays a key role in performance, even if you just use random labels, this is much better than no labels at all.
> * additional results show that selecting random labels from a true distribution of labels (instead of a uniform distribution) also helps.

文中接下来给出的示例，好像是针对第2条建议的，但是不太肯定

### Chain-of-Thought Prompting / 链式思考提示

链式思考（CoT）提示通过中间推理步骤实现了复杂的推理能力。最初，链式思考是与小样本提示结合使用的？  
之后，发展出了零样本链式思考，依靠`Let's think step by step.`这个提示来实现。  
英文版中最后提到的“Automatic Chain-of-Thought (Auto-CoT)”目前还没有翻译。我的理解是，通过Zero-Shot-CoT的方式，让大语言模型生成推理链，然后用这些推理链作为示例，用于小样本链式思考。可能在某些任务上，零样本链式思考还不够好，需要小样本链式思考来帮助？

Automatic Chain-of-Thought (Auto-CoT)
> When applying chain-of-thought prompting with demonstrations, the process involves hand-crafting effective and diverse examples. This manual effort could lead to suboptimal solutions. Zhang et al. (2022)(opens in a new tab) propose an approach to eliminate manual efforts by leveraging LLMs with "Let's think step by step" prompt to generate reasoning chains for demonstrations one by one. This automatic process can still end up with mistakes in generated chains. To mitigate the effects of the mistakes, the diversity of demonstrations matter. This works proposes Auto-CoT, which samples questions with diversity and generates reasoning chains to construct the demonstrations.
> 
> Auto-CoT consists of two main stages:
> 
> Stage 1): question clustering: partition questions of a given dataset into a few clusters  
> Stage 2): demonstration sampling: select a representative question from each cluster and generate its reasoning chain using Zero-Shot-CoT with simple heuristics  
> The simple heuristics could be length of questions (e.g., 60 tokens) and number of steps in rationale (e.g., 5 reasoning steps). This encourages the model to use simple and accurate demonstrations.

自动链式思考（ChatGPT翻译）
> 当使用"链式思考提示"结合演示时，过程涉及手工制作有效且多样化的例子。这种手动努力可能会导致次优解。张等人(2022)提出了一种方法，通过利用带有"一步一步思考"提示的LLMs来自动生成演示的推理链，消除手动努力。这个自动过程仍然可能会出现生成链的错误。为了减轻错误的影响，演示的多样性很重要。这项工作提出了Auto-CoT，它通过多样性抽样问题并生成推理链来构建演示。
> 
> Auto-CoT包括两个主要阶段：
> 
> 阶段1）问题聚类：将给定数据集的问题划分为几个聚类  
> 阶段2）演示抽样：从每个聚类中选择一个代表性问题，并使用简单的启发式方法使用Zero-Shot-CoT生成其推理链  
> 这些简单的启发式方法可以是问题长度（例如，60个标记）和理性步骤的数量（例如5个推理步骤）。这鼓励模型使用简单而准确的演示。

### Self-Consistency / 自我一致性

_Why_
> The idea is to sample multiple, diverse reasoning paths through few-shot CoT, and use the generations to select the most consistent answer. This helps to boost the performance of CoT prompting on tasks involving arithmetic and commonsense reasoning.  
> 该想法是通过小样本CoT采样多个不同的推理路径，并使用生成结果选择最一致的答案。这有助于提高CoT提示在涉及算术和常识推理的任务中的性能。

_How_
> TODO

_Question_

对于本章节的示例我还不太懂，针对一个提示，有3次不同的输出，其中两次输出正确，一次输出错误。  
这里为什么会有3次输出？反复输出n次的目的是什么？  
为什么输出结果会出现偏差？  
以及下面这句话想表达什么意思？是不是说，模型会在3次输出中找到两次正确的答案（或者说一致的答案），然后生成最终答案？  
> Computing for the final answer involves a few steps (check out the paper for the details) but for the sake of simplicity, we can see that there is already a majority answer emerging so that would essentially become the final answer.

### Generated Knowledge Prompting / 生成知识提示

_Why_
> 通过融合知识或信息，可以帮助大语言模型做出更准确的预测，特别是帮助提升常识推理类任务的表现。基于这种思路，可以先用模型生成知识，然后再做出预测。

_How_
> 1. 通过小样本提示生成相关知识
> 1. 将生成的知识与问题结合，进行提问

### Tree of Thoughts (ToT) / 思维树（ToT）

_Why_
> For complex tasks that require exploration or strategic lookahead, traditional or simple prompting techniques fall short. Yao et el. (2023)(opens in a new tab) and Long (2023)(opens in a new tab) recently proposed Tree of Thoughts (ToT), a framework that generalizes over chain-of-thought prompting and encourages exploration over thoughts that serve as intermediate steps for general problem solving with language models.
> 
> 对于需要探索或预判战略的复杂任务来说，传统或简单的提示技巧是不够的。最近，Yao et el. (2023)(opens in a new tab) 提出了思维树（Tree of Thoughts，ToT）框架，该框架基于思维链提示进行了总结，引导语言模型探索把思维作为中间步骤来解决通用问题。

_How_
> 思维树可以被总结为一段提示，如下：
> 
> Imagine three different experts are answering this question.  
> All experts will write down 1 step of their thinking,  
> then share it with the group.  
> Then all experts will go on to the next step, etc.  
> If any expert realises they're wrong at any point then they leave.  
> The question is...

### Retrieval Augmented Generation (RAG) / 检索增强生成（RAG）

_Why_
> For more complex and knowledge-intensive tasks, it's possible to build a language model-based system that accesses external knowledge sources to complete tasks. This enables more factual consistency, improves reliability of the generated responses, and helps to mitigate the problem of "hallucination".
> 
> 要完成更复杂和知识密集型的任务，可以基于语言模型构建一个系统，访问外部知识源来做到。这样的实现与事实更加一性，生成的答案更可靠，还有助于缓解“幻觉”问题。

_How_
> RAG takes an input and retrieves a set of relevant/supporting documents given a source (e.g., Wikipedia). The documents are concatenated as context with the original input prompt and fed to the text generator which produces the final output. This makes RAG adaptive for situations where facts could evolve over time. This is very useful as LLMs's parametric knowledge is static. RAG allows language models to bypass retraining, enabling access to the latest information for generating reliable outputs via retrieval-based generation.
> 
> RAG 会接受输入并检索出一组相关/支撑的文档，并给出文档的来源（例如维基百科）。这些文档作为上下文和输入的原始提示词组合，送给文本生成器得到最终的输出。这样 RAG 更加适应事实会随时间变化的情况。这非常有用，因为 LLM 的参数化知识是静态的。RAG 让语言模型不用重新训练就能够获取最新的信息，基于检索生成产生可靠的输出。

### Automatic Reasoning and Tool-use (ART) / 自动推理并使用工具（ART）

_Why_
> Combining CoT prompting and tools in an interleaved manner has shown to be a strong and robust approach to address many tasks with LLMs. These approaches typically require hand-crafting task-specific demonstrations and carefully scripted interleaving of model generations with tool use. Paranjape et al., (2023)(opens in a new tab) propose a new framework that uses a frozen LLM to automatically generate intermediate reasoning steps as a program.
> 
> 使用 LLM 完成任务时，交替运用 CoT 提示和工具已经被证明是一种即强大又稳健的方法。这类方法通常需要针对特定任务手写示范，还需要精心编写交替使用生成模型和工具的脚本。Paranjape et al., (2023)(opens in a new tab)提出了一个新框架，该框架使用冻结的 LLM 来自动生成包含中间推理步骤的程序。

_How_
> ART works as follows:
> 
> * given a new task, it select demonstrations of multi-step reasoning and tool use from a task library
> * at test time, it pauses generation whenever external tools are called, and integrate their output before resuming generation
> 
> ART的工作原理如下：
> 
> * 接到一个新任务的时候，从任务库中选择多步推理和使用工具的示范。
> * 在测试中，调用外部工具时，先暂停生成，将工具输出整合后继续接着生成。

## 其他

1. https://github.com/f/awesome-chatgpt-prompts
1. https://github.com/PlexPt/awesome-chatgpt-prompts-zh