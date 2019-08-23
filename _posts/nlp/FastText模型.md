---
title: FastText模型
date: 2019-08-13 21:23:10
tags: 
- NLP
- NLP模型理解
categories: 
- NLP
- 模型理解
---

[fastText原理及实践-我爱自然语言处理](http://www.52nlp.cn/fasttext)
[fastText原理-csdn](https://blog.csdn.net/feilong_csdn/article/details/88655927)

### softmax回归
- Softmax回归（Softmax Regression）又被称作多项逻辑回归（multinomial logistic regression），它是<font color='blue'>逻辑回归在处理多类别任务上的推广</font>
  - 代价函数上推导出它们的一致性
    {% asset_img resources/70870778EF50922ACDF626A7144504B3.jpg %}
- softmax函数常在神经网络输出层充当激活函数，<font color='blue'>目的就是将输出层的值归一化到0-1区间，将神经元输出构造成概率分布</font>，主要就是起到将神经元输出值进行归一化的作用


### FastText特点:分层softmax
- 背景：标准的Softmax回归中，要计算y=j时的Softmax概率，我们需要对所有的K个概率做归一化，这在|y|很大时非常耗时。于是，分层Softmax诞生了，它的基本思想是使用树的层级结构替代扁平化的标准Softmax。这样大大节省了时间空间
- [霍夫曼树理解](https://www.jianshu.com/p/5ad3e97d54a3)
  - 给定n个权值作为n个叶子结点，构造一棵二叉树，若带权路径长度达到最小，称这样的二叉树为最优二叉树，也称为霍夫曼树
  {% asset_img resources/430068DB5AC121047B4A3C25A8011B41.jpg %}
- 构造霍夫曼树方法：根据类标的频数构造的霍夫曼树
  - 表示出现最频繁的类，路径最短
  {% asset_img resources/C35F9C831B7AD4E037FCA1E1280C07AB.jpg %}
- 使用：当我们知道了目标类别(或者单词)x，之后，我们只需要计算root节点，到该词的路径累乘，即可. 不需要去遍历所有的节点信息，时间复杂度变为O(log2(V))
  {% asset_img resources/96F7AC4147123C58558306B052E36EF1.jpg %}
- [单词预测举例](https://cloud.tencent.com/developer/article/1387413)
  {% asset_img resources/B67415B091A6681D9725113B7FC621EB.jpg %}

### FastText特点:n-gram特征
- 在文本特征提取中，常常能看到n-gram的身影。它是一种基于语言模型的算法，基本思想是将文本内容按照字节顺序进行大小为N的滑动窗口操作，最终形成长度为N的字节片段序列
  - 分字粒度和词粒度
- n-gram产生的特征只是作为文本特征的候选集，你后面可能会采用信息熵、卡方统计、IDF等文本特征选择方式筛选出比较重要特征
- 优势：
  - 对于低频词生成的词向量效果会更好。因为它们的n-gram可以和其它词共享。
  - 对于训练词库之外的单词，仍然可以构建它们的词向量。我们可以叠加它们的字符级n-gram向量。

### word2vec:cbow
- 背景:word2vec主要有两种模型：skip-gram 模型和CBOW模型,CBOW模型架构和fastText模型非常相似
- CBOW:
  - CBOW模型的基本思路是：用上下文预测目标词汇
  - 架构图如下所示：
    {% asset_img resources/356A3D7957EBFAF7B31C291320A7E1CD.jpg %}
  - 神经网络模型：
    <font color='red'>重点就是学习输入层到隐藏层，隐藏层到输出层的权重矩阵</font>
    - 输入层：输入层由目标词汇y的上下文单词{${x_1,x_2...x_c}$}组成，$x_i$是被onehot编码过的V维向量，其中V是词汇量
    - 隐含层：隐含层是N维向量h
    - 输出层：输出层是被onehot编码过的目标词y
    因为词库V往往非常大，使用标准的softmax计算相当耗时，于是CBOW的输出层采用的正是上文提到过的分层Softmax

### FastText
- 使用fastText进行文本分类的<font color='red'>同时也会产生词的embedding</font>，即embedding是fastText分类的产物。除非你决定使用预训练的embedding来训练fastText分类模型

#### 对word2vec的改进
  - 背景：
    word2vec把语料库中的每个单词当成原子的，它会为每个单词生成一个向量。这忽略了单词内部的形态特征，比如：“apple” 和“apples”，“达观数据”和“达观”，这两个例子中，两个单词都有较多公共字符，即它们的内部形态类似，但是在传统的word2vec中，这种单词内部形态信息因为它们被转换成不同的id丢失了
  - fasttext方法不同与word2vec方法，引入了两类特征并进行embedding。<font color='blue'>其中n-gram颗粒度是词与词之间，n-char是单个词之间</font>
    - n-char:
      对于单词“apple”，假设n的取值为3，则它的trigram有:
“<ap”,  “app”,  “ppl”,  “ple”, “le>”
  其中，<表示前缀，>表示后缀。于是，我们可以用这些trigram来表示“apple”这个单词，进一步
    - 我们可以用这5个trigram的向量叠加来表示“apple”的词向量
    

#### 模型架构
  {% asset_img resources/BB48BB14D0F15156AEAA48EAF5F99498.jpg %}
  - 不同点1：输入
    - CBOW的输入是目标单词的上下文，fastText的输入是多个单词及其n-gram或者n-char特征，这些特征用来表示单个文档
    - CBOW的输入单词被onehot编码过，fastText的输入特征是被embedding过
  - 不同点2：输出
    - CBOW的输出是目标词汇，fastText的输出是文档对应的类标
    - fastText采用了分层Softmax，大大降低了模型训练时间
    

#### 核心思想：
- 仔细观察模型的后半部分，即从隐含层输出到输出层输出，会发现它<font color='blue'>就是一个softmax线性多类别分类器</font>，分类器的输入是一个用来表征当前文档的向量；模型的前半部分，即从输入层输入到隐含层输出部分，主要在做一件事情：<font color='blue'>生成用来表征文档的向量</font>。那么它是如何做的呢？叠加构成这篇文档的所有词及n-gram的词向量，然后取平均。叠加词向量背后的思想就是传统的词袋法，即将文档看成一个由词构成的集合
  - <font color='red'>将整篇文档的词及n-gram向量叠加平均得到文档向量，然后使用文档向量做softmax多分类
    - 这中间涉及到两个技巧：字符级n-gram特征的引入以及分层Softmax分类</font>
  