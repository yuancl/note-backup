---
title: BERT模型
date: 2019-01-06 19:34:10
tags: 
- NLP
- NLP模型理解
categories: 
- NLP
- 模型理解
---

首先理解：
[Attention模型](https://yuancl.github.io/2019/01/05/nlp/Attention模型/)
[Transformer模型](https://yuancl.github.io/2019/01/06/nlp/Transformer模型/)

参考文章：
[从Word Embedding到Bert模型——自然语言处理预训练技术发展史](https://blog.csdn.net/malefactor/article/details/83961886)
[谷歌终于开源BERT代码：3 亿参数量，机器之心全面解读](https://www.jiqizhixin.com/articles/2018-11-01-9?from=synced)

#### 概览
- 效果：Bert 具备广泛的通用性，就是说绝大部分 NLP 任务都可以采用类似的两阶段模式直接去提升效果

#### 预训练发展史:
#### ELMO(Embedding from Language Models)
- 参考图像领域预训练[预训练理解](https://yuancl.github.io/2018/10/10/dl/第三门课-第二周/)
- 词嵌入[Word Embedding,NNLM,Word2Vec(CBOW,Skip-gram),Glove](https://yuancl.github.io/2018/12/01/dl/第五门课-第二周/)

- 解决问题：Work Embedding的多义性问题
  - 静态的方式:训练好之后每个单词的表达就固定住了，以后使用的时候，不论新句子上下文单词是什么，这个单词的 Word Embedding 不会跟着上下文场景的变化而改变，所以对于比如 Bank 这个词
- 本质思想
  - 用事先用语言模型学好一个单词的 Word Embedding，然后根据当前上下文对 Word Embedding 动态调整的思路
- 两阶段：
  - 第一个阶段是利用语言模型进行预训练
  - 第二个阶段是在做下游任务时，从预训练网络中提取对应单词的网络各层的 Word Embedding 作为<font color='blue'>新特征</font>补充到下游任务中
    - Feature-based Pre-Training
      因为 ELMO给下游提供的是每个单词的特征形式，所以这一类预训练的方法被称为“Feature-based Pre-Training”
  {% asset_img resources/A28E00CA5B362C88488EABCE29B9311F.jpg %}
- ELMO和图像预训练的区别
  - ELMO 代表的这种基于特征融合的预训练方法
  - NLP还有一种：基于 Fine-tuning 的模式,而 GPT 就是这一模式的典型开创者，这种和图像预训练比较像
- ELMO有什么缺点(GPT和Bert出来之后对比)
  - LSTM抽取特征能力远低于[Transformer](https://yuancl.github.io/2019/01/06/nlp/Transformer模型/)
  - 拼接方式双向融合特征融合能力偏弱
  

#### GPT(Generative Pre-Training)
  - 和ELMO区别
    - 使用的Fine-tuning模式,特征抽取器不是用的 RNN，而是用的 [Transformer](https://yuancl.github.io/2019/01/06/nlp/Transformer模型/)
    - GPT 的预训练虽然仍然是以语言模型作为目标任务，但是采用的是单向的语言模型
      - 这限制了其在更多应用场景的效果，比如阅读理解这种任务，在做任务的时候是可以允许同时看到上文和下文一起做决策的
    - 下游使用(Fine-tuning)
      - 要向 GPT 的网络结构看齐，把任务的网络结构改造成和 GPT 的网络结构是一样的。然后，在做下游任务的时候，利用第一步预训练好的参数初始化 GPT 的网络结构
  {% asset_img resources/2627CF7F4812570097CDA2E24151AC93.jpg %}

  - 下游任务如何改造靠近 GPT 的网络结构呢
    {% asset_img resources/D424E7A94A3A8AFA95BF948622EBF372.jpg %}
    
  - GPT有什么问题:
    - 最主要的就是那个单向语言模型
  

#### Bert
- 两阶段模型
  - 在预训练阶段采用了类似 ELMO 的双向语言模型，和GPT一样使用[Transformer](https://yuancl.github.io/2019/01/06/nlp/Transformer模型/)网络
  - Fine-Tuning 阶段，这个阶段的做法和 GPT 是一样的
    
- 和GPT,ELMO关系
  - 如果我们把 GPT预训练阶段换成双向语言模型，那么就得到了 Bert
  - 如果我们把 ELMO 的特征抽取器换成[Transformer](https://yuancl.github.io/2019/01/06/nlp/Transformer模型/)，那么我们也会得到 Bert
  {% asset_img resources/E701F5F85BCFD4AE2C7F2ABE1AA7AE4E.jpg %}

- 输入表征
  BERT 最核心的过程就是同时预测加了 MASK 的缺失词与 A/B 句之间的二元关系，而这些首先都需要体现在模型的输入中
  - 特殊符 [SEP] 是用于分割两个句子的符号
  - 前面半句会加上分割编码 A，后半句会加上分割编码 B,预测 B 句是不是 A 句后面的一句话
  - 为了令 Transformer感知词与词之间的位置关系，我们需要使用位置编码给每个词加上位置信息
  {% asset_img resources/56096F6E25AD52E2E76CF6E2649085FB.jpg %}

- 预训练过程
  BERT 最核心的就是预训练过程，这也是该论文的亮点所在。简单而言，模型会从数据集抽取两句话，其中B句有 50% 的概率是 A句的下一句，然后将这两句话转化前面所示的输入表征。现在我们<font color='blue'>随机遮掩（Mask 掉）</font>输入序列中 15% 的词，并<font color='red'>要求 Transformer 预测这些被遮掩的词，以及 B 句是 A 句下一句的概率这两个任务</font>
  - 对于二分类任务，在抽取一个序列（A+B）中，B 有 50% 的概率是 A 的下一句。如果是的话就会生成标注「IsNext」，不是的话就会生成标注「NotNext」，这些标注可以作为二元分类任务判断模型预测的凭证
  - 对于 Mask 预测任务，首先整个序列会随机 Mask 掉 15% 的词，这里的 Mask 不只是简单地用「[MASK]」符号代替某些词，因为这会引起预训练与微调两阶段不是太匹配。所以谷歌在确定需要 Mask 掉的词后，80% 的情况下会直接替代为「[MASK]」，10% 的情况会替代为其它任意的词，最后 10% 的情况会保留原词
  {% asset_img resources/368B1596DC8027EAB8592E9AC0B1D5C2.jpg %}
  
- 微调过程
  - 下图展示了 BERT 在 11 种任务中的微调方法，它们都只添加了一个额外的输出层。在下图中，Tok 表示不同的词、E 表示输入的嵌入向量、T_i 表示第 i 个词在经过 BERT 处理后输出的上下文向量
  {% asset_img resources/90D75BE758BD70EE89D79AB4258221C9.jpg %}
    - (a)中判断问答对是不是包含正确回答的 QNLI、判断两句话有多少相似性的 STS-B 等，它们都用于处理句子之间的关系
    - (b)中判语句中断情感趋向的 SST-2 和判断语法正确性的 CoLA 任务，它们都是处理句子内部的关系
    
  - NLP四类任务
    - 一类是序列标注，这是最典型的 NLP 任务，比如中文分词，词性标注，命名实体识别，语义角色标注等都可以归入这一类问题，它的特点是句子中每个单词要求模型根据上下文都要给出一个分类类别。
    - 第二类是分类任务，比如我们常见的文本分类，情感计算等都可以归入这一类。它的特点是不管文章有多长，总体给出一个分类类别即可。
    - 第三类任务是句子关系判断，比如 Entailment，QA，语义改写，自然语言推理等任务都是这个模式，它的特点是给定两个句子，模型判断出两个句子是否具备某种语义关系。
    - 第四类是生成式任务，比如机器翻译，文本摘要，写诗造句，看图说话等都属于这一类。它的特点是输入文本内容后，需要自主生成另外一段文字
    - {% asset_img resources/52B6633F1E96B86DC6B4477CA91629D3.jpg %}
    
  {% asset_img resources/C8C2770559C89A232F97E4DCA20834D3.jpg %}
  
  