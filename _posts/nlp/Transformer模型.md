---
title: Transformer模型
date: 2019-01-06 18:23:10
tags: 
- NLP
- NLP模型理解
categories: 
- NLP
- 模型理解
---

参考文章：
[神经机器翻译 之 谷歌 transformer 模型](https://shimo.im/docs/gmRW4WV2mjoXzKA1)
[Self-attention and Transformer](https://www.cnblogs.com/robert-dlut/p/8638283.html)
[大数据文摘-BERT大火却不懂Transformer](https://mp.weixin.qq.com/s?__biz=MjM5MTQzNzU2NA==&mid=2651666707&idx=1&sn=2e9149ccdba746eaec687038ce560349&chksm=bd4c1e808a3b97968a15cb3d21032b5394461a1be275476e4fd26563aa28d99be0b798ccee17&mpshare=1&scene=1&srcid=0111kbdci7utfkYpw9bNBcpF&key=f8b9d5856fa70f7d2eabd677b381f98687650f8caf1af873c78466b32517ee5af4eecc661ae63d35bf90beca422d1abea7b7c897e43f33ab3ef7de4c816797d4bad752a5e6f5acc1908b28ffd604355f&ascene=0&uin=MTE0NTY4MjMyMQ%3D%3D&devicetype=iMac+MacBookPro14%2C1+OSX+OSX+10.13.6+build(17G65)&version=11020201&lang=zh_CN&pass_ticket=sLCET0Y%2BZTkYDsKSej3nfbOS885niL2%2Bt2ffNlFmQw3FszFuawe4q3nwl02gUnCe)


#### 背景
- 首先理解[Attention模型](https://yuancl.github.io/2019/01/05/nlp/Attention模型/)，个人理解Transformer本质其实就是对selt-attention的包装
- 论文《Attention is all you need》特点：
  重点关注了复杂度，并行度，长距离依赖学习三个问题
  - 现在做神经翻译里最好的BLUE结果
  - 没有采取大热的RNN/LSTM/GRU的结构，而是使用attention layer 和全连接层，达到了较好的效果，并且解决了 RNN/LSTM/GRU 里的long dependency problem 
  - 解决了传统RNN 训练并行度的问题，并降低了计算复杂度


#### Encoder-Decoder架构
- Encoder-Decoder架构整体
  - 在编码器的一个网络块中，由一个多头attention子层和一个前馈神经网络子层组成，整个编码器栈式搭建了N个块
  - 解码器的一个网络块中多了一个多头attention层。为了更好的优化深度网络，整个网络使用了残差连接和对层进行了规范化（Add&Norm）
    - 这里有个特别点就是masking,  masking 的作用就是防止在训练的时候 使用未来的输出的单词。 比如训练时， 第一个单词是不能参考第二个单词的生成结果的。 Masking就会把这个信息变成0， 用来保证预测位置 i 的信息只能基于比 i 小的输出
{% asset_img resources/C421D21BFAA4597BC61F35AC079AF098.jpg %}

- Encode组件
  {% asset_img resources/93027EA8E8E28E2249EA065D24A54A0F.jpg %}

- Decode组件
  - 编码器通过处理输入序列开启工作。顶端编码器的输出之后会变转化为一个包含向量K（键向量）和V（值向量）的注意力向量集 。这些向量将被每个解码器用于自身的“编码-解码注意力层”，而这些层可以帮助解码器关注输入序列哪些位置合适
  {% asset_img resources/B464700544F8040B5FAC358F5070AEE0.gif %}
  - 接下来的步骤重复了这个过程，直到到达一个特殊的终止符号，  -  它表示transformer的解码器已经完成了它的输出。每个步骤的输出在下一个时间步被提供给底端解码器，并且就像编码器之前做的那样，这些解码器会输出它们的解码结果
  - 这个“编码-解码注意力层”工作方式基本就像多头自注意力层一样，只不过它是通过在它下面的层来创造查询矩阵，并且从编码器的输出中取得键/值矩阵
  <p style="margin-left: 8px;margin-right: 8px;"><span data-ratio="1.9636363636363636" id="js_tx_video_container_0.7003880785346812" class="js_tx_video_container" style="display: block; width: 661px; height: 372px;"><iframe frameborder="0" width="661" height="371.8125" allow="autoplay; fullscreen" allowfullscreen="true" src="//v.qq.com/txp/iframe/player.html?origin=https%3A%2F%2Fmp.weixin.qq.com&amp;vid=m13563cy49o&amp;autoplay=false&amp;full=true&amp;show1080p=false&amp;isDebugIframe=false"></iframe></span></p>

- 最终的线性变换和Softmax层
  解码组件最后会输出一个实数向量。我们如何把浮点数变成一个单词？这便是线性变换层要做的工作，它之后就是Softmax层
  - 线性变换层是一个简单的全连接神经网络，它可以把解码组件产生的向量投射到一个比它大得多的、被称作对数几率（logits）的向量里
  - 接下来的Softmax 层便会把那些分数变成概率（都为正数、上限1.0）。概率最高的单元格被选中，并且它对应的单词被作为这个时间步的输出
  {% asset_img resources/1493FFA7472D4E31F49EF536CF6F1F54.jpg %}

#### scaled dot-Product attention
- 本质：其实scaled dot-Product attention就是我们常用的使用<font color='blue'>点积进行相似度计算</font>的attention，只是多除了一个（为K的维度）起到调节作用，使得内积不至于太大
- 操作步骤
  - 每个query-key 会做出一个点乘的运算过程
  - 最后会使用soft max 把他们归一
  - 再到最后会乘以V (values) 用来当做attention vector. 
  {% asset_img resources/70C79702B44D8406D4B78338B0C7ADAD.jpg %}
- 详细理解
  {% asset_img resources/F21545F043FA58B804CAEC5D3C7EF995.jpg %}


#### Multi-head attention(1)
- 它扩展了模型专注于不同位置的能力
  - <font color='blue'>我理解就是不同的每个注意力头都会对会有不同的关注点,就是丰富了一个词的注意点</font>
- 它给出了注意力层的多个“表示子空间”（representation subspaces）
  - 接下来我们将看到，对于“多头”注意机制，我们有多个查询/键/值权重矩阵集(Transformer使用八个注意力头，因此我们对于每个编码器/解码器有八个矩阵集合)。这些集合中的每一个都是随机初始化的
  - 在训练之后，每个集合都被用来将输入词嵌入(或来自较低编码器/解码器的向量)<font color='blue'>投影到不同的表示子空间中</font>
 {% asset_img resources/68E2D18A10963F046370DB126E452A3F.jpg %}

- 如果我们做与上述相同的自注意力计算，只需八次不同的权重矩阵运算，我们就会得到八个不同的Z矩阵
  {% asset_img resources/881CCDA1BDBA993EA7665D2F8B9FC3A9.jpg %}

- <font color='blue'>前馈层不需要8个矩阵，它只需要一个矩阵</font>(由每一个单词的表示向量组成)
  - 所以我们需要一种方法把这八个矩阵压缩成一个矩阵。那该怎么做？其实可以直接把这些矩阵拼接在一起，然后用一个附加的权重矩阵WO与它们相乘
{% asset_img resources/7903100853302A431B813728D73BBFA0.jpg %}

#### Multi-head attention(2)
- Query，Key，Value首先进过一个线性变换
- 然后输入到放缩点积attention
  - 注意这里要做h次，其实也就是所谓的多头，每一次算一个头
  - 每次Q，K，V进行线性变换的参数W是不一样的，所以每个头都计算自己的特征
  - 进行h个头计算好处：好处是可以允许模型在不同的表示子空间里学习到相关的信息
- 然后将h次的放缩点积attention结果进行拼接(concat)
- 再进行一次线性变换得到的值作为多头attention的结果

{% asset_img resources/8D8F43D711E9C94BA7013FD1DE9A666F.jpg %}


#### 如何使用attention
- 首先在编码器到解码器的地方使用了多头attention进行连接,编码器的层输出（这里K=V）和解码器中都头attention的输入。其实就和主流的机器翻译模型中的attention一样
  {% asset_img resources/F318057213E0EE97E53E6A165DF5E892.jpg %}
- 在Encoder,Decoder中都使用的self-attention
  - 例如输入一个句子，那么里面的每个词都要和该句子中的<font color='blue'>所有词进行attention计算</font>。目的是<font color='blue'>学习句子内部的词依赖关系，捕获句子的内部结构</font>
{% asset_img resources/A60F81F9218EB99D06E9DB202DB6F8AD.jpg %}

#### Transformer解决问题
- 并行问题
  - 首先each head， 是可以并行计算的， 然后每个head 都有自己对应的weight, 实现不同的线性转换， 这样每个head 也就有了自己特别的表达信息
  - 多头attention和CNN一样不依赖于前一时刻的计算，可以很好的并行，优于RNN
- 长距离依赖学习
  - self-attention是每个词和所有词都要计算attention，所以不管他们中间有多长距离，最大的路径长度也都只是1。可以捕获长距离依赖关系
- RNN，CNN计算复杂度的比较
  - 如果输入序列n小于表示维度d的话，每一层的时间复杂度self-attention是比较有优势的。当n比较大时，作者也给出了一种解决方案self-attention（restricted）即每个词不是和所有词计算attention，而是<font color='blue'>只与限制的r个词去计算attention</font>
  

#### 训练部分&损失函数
Transformer只是一个encode-decode框架架构，后面的，训练&损失函数&优化算法就也能共用其他了，比如BP，集束搜索(beam search)...
- 既然我们已经过了一遍完整的transformer的前向传播过程，那我们就可以直观感受一下它的训练过程。
- 在训练过程中，一个未经训练的模型会通过一个完全一样的前向传播。但因为我们用有标记的训练集来训练它，所以我们可以用它的输出去与真实的输出做比较
