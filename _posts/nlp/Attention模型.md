---
title: Attention模型
date: 2019-01-05 18:41:10
tags: 
- NLP
- NLP模型理解
categories: 
- NLP
- 模型理解
---

[吴恩达课程-Attention实现](https://yuancl.github.io/2018/12/06/dl/第五门课-第三周/)
参考文章：[深度学习中的注意力模型](https://zhuanlan.zhihu.com/p/37601161)

#### Encoder-Decoder框架
- 可以把它看作适合处理由一个句子（或篇章）生成另外一个句子（或篇章）的通用处理模型
- Encoder顾名思义就是对输入句子X进行编码，将输入句子通过非线性变换转化为中间语义表示C：$C=F(X_1,X_2,...X_m)$
- 解码器Decoder来说，其任务是根据句子X的中间语义表示C和之前已经生成的历史信息y1,y2….yi-1来生成i时刻要生成的单词yi $y_i=g(c,y_1,y_2,...y_{i-1})$
- 总结：
  - Encoder-Decoder是个非常通用的计算框架，常见的比如CNN/RNN/BiRNN/GRU/LSTM/Deep LSTM等，这里的变化组合非常多
  - 框架很多应用场景：
    - 机器翻译来说，<X,Y>就是对应不同语言的句子，比如X是英语句子，Y是对应的中文句子翻译
    - 对于文本摘要来说，X就是一篇文章，Y就是对应的摘要
    - 对话机器人来说，X就是某人的一句话，Y就是对话机器人的应答
  
{% asset_img resources/F2F31AE3AFCF5E63870687850E25053E.jpg %}

#### Attention模型
- 分心模型：在生成目标句子的单词时，不论生成哪个单词，是y1,y2也好，还是y3也好，他们使用的句子X的<font color='blue'>语义编码C都是一样的，没有任何区别。</font>
- 添加注意力：$y_i的输出对应不同的输入c_i$
  {% asset_img resources/24F1729383B4C502BDF631EC89F3ED8D.jpg %}
- 例如生成$y_3$:汤姆这个词的对应输入词的权重：
  {% asset_img resources/3C10CE8870D6AD2D2C85274AFB51FC42.jpg %}
- 如何确定这些权重？(可以看下面的三阶段计算Attention过程)
  - 通过函数F(hj,Hi)来获得目标单词Yi和每个输入单词对应的对齐可能性(<font color='blue'>其实也是通过一个函数获取各个单词的权重</font>)
    - 对于采用RNN的Decoder来说，如果要生成yi单词，在时刻i，我们是可以知道在生成Yi之前的隐层节点i时刻的输出值Hi的，而我们的目的是要计算生成Yi时的输入句子单词“Tom”、“Chase”、“Jerry”对Yi来说的注意力分配概率分布。那么可以用i时刻的隐层节点状态Hi去一一和输入句子中每个单词对应的RNN隐层节点状态hj进行对比，即通过函数F(hj,Hi)来获得目标单词Yi和每个输入单词对应的对齐可能性
  - 这个F函数在不同论文里可能会采取不同的方法，然后函数F的输出经过Softmax进行归一化就得到了符合概率分布取值区间的注意力分配概率分布数值

- 直观理解
  - 目标句子生成的每个单词对应输入句子单词的概率分布可以理解为输入句子单词和这个目标生成单词的对齐概率，这在机器翻译语境下是非常直观的：传统的统计机器翻译一般在做的过程中会专门有一个短语对齐的步骤，而注意力模型其实起的是相同的作用
  - 从概念上理解的话，把AM模型理解成影响力模型也是合理的，就是说生成目标单词的时候，输入句子每个单词对于生成这个单词有多大的影响程度
    - 例子：矩阵中每一列代表生成的目标单词对应输入句子每个单词的AM分配概率，颜色越深代表分配到的概率越大
    {% asset_img resources/339E562920007C2CC0BD095CE03620E4.jpg %}

- Attention机制的本质思想
  我们可以这样来看待Attention机制（参考图9）：将Source中的构成元素想象成是由一系列的<Key,Value>数据对构成，此时给定Target中的某个元素Query，通过计算Query和各个Key的相似性或者相关性，得到每个Key对应Value的权重系数，<font color='blue'>然后对Value进行加权求和，即得到了最终的Attention数值</font>。<font color='red'>所以本质上Attention机制是对Source中元素的Value值进行加权求和</font>，而Query和Key用来计算对应Value的权重系数。即可以将其本质思想改写为如下公式:
  {% asset_img resources/B514B9C65D76AEC9E2D3C1BA0E1714FC.jpg %}
  {% asset_img resources/08DC337F6EEC1B4ECCCE3069BCF2DEE8.jpg %}
  
- 三阶段计算Attention过程
  - 第一个阶段根据Query和Key计算两者的相似性或者相关性
    - 可以引入不同的函数和计算机制，根据Query和某个Key_i，计算两者的相似性或者相关性，最常见的方法包括：求两者的向量点积、求两者的向量Cosine相似性或者通过再引入额外的神经网络来求值
    {% asset_img resources/C46D9EBEDC7DC8BDD2FD25C876452832.jpg %}
  - 第二个阶段对第一阶段的原始分值进行归一化处理
    - 引入类似SoftMax的计算方式对第一阶段的得分进行数值转换，一方面可以进行归一化，将原始计算分值整理成所有元素权重之和为1的概率分布
    - 另一方面也可以通过SoftMax的内在机制更加突出重要元素的权重
    {% asset_img resources/CFA555D76CFF7EF83BB388D9DCC83E83.jpg %}
  - 第三个阶段根据权重系数对Value进行加权求和
    - 计算结果a_i即为value_i对应的权重系数，然后进行加权求和即可得到Attention数值
    {% asset_img resources/670426A82FAECEDC11036020CAE91B6B.jpg %}
  {% asset_img resources/B0A35F4001A8D56A581543FF91E10844.jpg %}
  - 总结：通过如上三个阶段的计算，即可求出针对Query的Attention数值，目前绝大多数具体的注意力机制计算方法都符合上述的三阶段抽象计算过程
  

#### 从微观视角看自注意力机制
- 第一步就是从每个编码器的输入向量（每个单词的词向量）中生成三个向量
  - 我们创造一个<font color='blue'>查询向量、一个键向量和一个值向量。这三个向量是通过词嵌入与三个权重矩阵后相乘创建的</font>
  - 这些新向量在维度上比词嵌入向量更低。他们的维度是64，而词嵌入和编码器的输入/输出向量的维度是512
  {% asset_img resources/A6F39C8F454935AAFEBB83A0ACF2E1B0.jpg %}
- 第二步是计算得分
  - 假设我们在为这个例子中的第一个词“Thinking”计算自注意力向量，我们需要拿输入句子中的每个单词对“Thinking”打分。<font color='blue'>这些分数决定了在编码单词“Thinking”的过程中有多重视句子的其它部分</font>
    - 这些分数是通过打分单词（所有输入句子的单词）的键向量与“Thinking”的查询向量相点积来计算的
    - 所以如果我们是处理位置最靠前的词的自注意力的话，第一个分数是q1和k1的点积，第二个分数是q1和k2的点积
  {% asset_img resources/807C3954F1BF0F7C006209437905F305.jpg %}
- 第三步第四步
  - 将分数除以8(8是论文中使用的键向量的维数64的平方根，这会让梯度更稳定。这里也可以使用其它值，8只是默认值)
  - 然后通过softmax传递结果。<font color='blue'>softmax的作用是使所有单词的分数归一化，得到的分数都是正值且和为1</font>
    - 这个softmax分数决定了每个单词对编码当下位置（“Thinking”）的贡献。显然，已经在这个位置上的单词将获得最高的softmax分数
  {% asset_img resources/EED7B3888E7ED2122DF172489F13BE4C.jpg %}
- 第五步是将每个值向量乘以softmax分数(这是为了准备之后将它们求和)。
  - 这里的直觉是<font color='blue'>希望关注语义上相关的单词，并弱化不相关的单词</font>(例如，让它们乘以0.001这样的小数)
- 第六步是对加权值向量求和，然后即得到自注意力层在该位置的输出(在我们的例子中是对于第一个单词)
  - 译注：自注意力的另一种解释就是在编码某个单词时，就是将所有单词的表示（值向量）进行加权求和，而权重是通过该词的表示（键向量）与被编码词表示（查询向量）的点积并通过softmax得到。
  {% asset_img resources/1748301F371EF3428E3F4DB1A5F47730.jpg %}




#### Self Attention模型
- 一般任务中的情况
  在一般任务的Encoder-Decoder框架中，输入Source和输出Target内容是不一样的，比如对于英-中机器翻译来说，Source是英文句子，Target是对应的翻译出的中文句子，<font color='blue'>Attention机制发生在Target的元素Query和Source中的所有元素之间</font>
- Self Attention
  Attention顾名思义，指的不是Target和Source之间的Attention机制，<font color='blue'>而是Source内部元素之间或者Target内部元素之间发生的Attention机制，也可以理解为Target=Source这种特殊情况下的注意力计算机制</font>。其具体计算过程是一样的，只是计算对象发生了变化而已，所以此处不再赘述其计算过程细节
- 例子
  {% asset_img resources/78832378181B08156139C411039FF734.jpg %}
- 优点：
  - 更擅长捕获句子中长距离特征
    引入Self Attention后会更容易捕获句子中长距离的相互依赖的特征，因为如果是RNN或者LSTM，需要依次序序列计算，对于远距离的相互依赖的特征，要经过若干时间步步骤的信息累积才能将两者联系起来，而距离越远，有效捕获的可能性越小
    - 但是SelfAttention在计算过程中会直接将句子中任意两个单词的联系通过一个计算步骤直接联系起来，所以远距离依赖特征之间的距离被极大缩短
  - 增加计算的并行性
  
  
#### Attention机制的应用
Attention机制不仅在NLP有广泛应用，在图像和语言领域也有很多的应用
- 图片描述
  - 很明显这种应用场景也可以使用Encoder-Decoder框架来解决任务目标，此时Encoder输入部分是一张图片，一般会用CNN来对图片进行特征抽取，Decoder部分使用RNN或者LSTM来输出自然语言句子
  - 此时如果加入Attention机制能够明显改善系统输出效果，Attention模型在这里起到了类似人类视觉选择性注意的机制，在输出某个实体单词的时候会将注意力焦点聚焦在图片中相应的区域上
  {% asset_img resources/D65829551454CDE399C3DBE51C953E17.jpg %}
- Attention让每个单词对应图片中的注意力聚焦区域
  {% asset_img resources/495110CA9218AF232B0D47BE88497073.jpg %}
- 语音识别中音频序列和输出字符之间的Attention
  - 展示了在Encoder-Decoder框架中加入Attention机制后，当用户用语音说句子how much would a woodchuck chuck 时，输入部分的声音特征信号和输出字符之间的注意力分配概率分布情况，颜色越深代表分配到的注意力概率越高。从图中可以看出，在这个场景下，Attention机制起到了将输出字符和输入语音信号进行对齐的功能
  {% asset_img resources/7F7EE44196F2FAAF3F1BC2BFAB29DA4A.jpg %}
- 总结：Encoder-Decoder加Attention架构由于其卓越的实际效果，目前在深度学习领域里得到了广泛的使用，了解并熟练使用这一架构对于解决实际问题会有极大帮助