---
title: GAN基础
date: 2019-01-10 22:10:12
tags: 
- 机器学习
- GAN
categories: 
-  机器学习
-  GAN
---

### Basic idea of GAN
#### Generator
{% asset_img resources/66AEF5EADDEC96BE3DA212E758624834.jpg %}
{% asset_img resources/9E2ECC940FEAF9C96D8CEE35311233A2.jpg %}

#### Discriminator(辨别者，鉴别器)
- 产出一个标量
{% asset_img resources/6998CF78D25699B4821C46E82C0998DE.jpg %}

#### 形容关系：猎食者和天敌都在净化
- 天敌-Discriminator
- 枯叶蝶-Generator
  - 枯叶蝶为了躲避猎食者的捕猎，不行进化自身
{% asset_img resources/8344AE0D169C537B6DF1D907BC7348D3.jpg %}

- 二次元也是一样的
  - Generator,Discriminator不断进化Discriminator2骗过Generator1，Discriminator3骗过Generator2等
  - 看起来Generator,Discriminator像是对抗的样子，所以是adversarial的由来
  {% asset_img resources/9D1A3879E4E766C5138C872EE7020170.jpg %}

- 对抗只是拟人的方法，下面就是和平的比喻
  - 问题：
    - 1.为什么Generator不能自己学,而需要Discriminator驱动
    - 2.为什么Discriminator不自己做
    {% asset_img resources/17AAF5A1E271847BB8A9565F19E6F561.jpg %}

#### Algorithm
- step1
  - 训练Discriminator，使database中产生的结果很接近1，Generator产生的结果很接近0
  {% asset_img resources/E91AB963E417F2AF849AA38157129079.jpg %}

- step2
  - Fix Discriminator,update Generator
  - Generator，Discriminator合一起，成一个巨大的网络，比如前几层是Generator，后几层是Discriminator
  - 这一步目标就是巨大网络输出scala值要大(Gradient Ascent)
    - 通常情况下，要让最后的输出很大，只需要调整最后一层softmax层就可以了
    - 但是这里固定住后面几层，只让调整Generator
  {% asset_img resources/F11F16B8F1D8F59E45D03467FD6AEE4A.jpg %}

- 整体算法
  - Learning D
    - sample from database:$x^m$,noise samples:$z^m$
    - $\tilde x^m=G(z^m)$表示Genertor产生的vector
    - 然后通过梯度提升算法，最优化$\tilde V$,含义就是让$D(x^i)尽量大,D(\tilde x^i)$尽量小
  - Learning G
    - 目的就是update G，使其能够骗过D
    - $D(G(z^i))$，理解其含义就是让noise数据经过G处理后，然后通过D（骗过D），得到最大的标量值
  {% asset_img resources/5E52567B26EE736450698E2A8BF45E80.jpg %}


### GAN as structured learning
#### Structured Learning
- 当输出不是一个标量数值或者分类，是更负责的模型的时候，比如seq，matrix，graph等
{% asset_img resources/436BF7130FE4A4A66B51511F88BF4640.jpg %}
- Why structured learning challenging
  - 必须考虑大局观
  {% asset_img resources/DE35D645A761A70023B790B3E4071884.jpg %}
- Structured Learning Approach
  - Bottom up方法容易失去大局观
    - 一个componet一个component地生成 
    - component与component之间的关系不容易把握
  - Top Down方法不容易train
{% asset_img resources/9C427F41E805E3C0017D49FA943E5150.jpg %}

###  Can Generator learn by itself
#### NN Generator和NN Classifier比较类似
- 只是一个是输入vector，一个输入图像
- 问题：NN Generator如何产生输入的vector(将图片进行编码)?
  - 不能够随机产生，因为如果随机产生，就无法表示出向量的相似性了(比如图片1有很多种，左斜，右斜等)
{% asset_img resources/B17EF3ED0C502169303D060B02B9803D.jpg %}

#### Auto Encoder-Decoder
- 解决上面：NN Generator如何产生输入vector问题
- 如图：encode模块，对输入和输出图片越接近越好
  {% asset_img resources/02DBAADA9FFA8D1FD2E7FB2CB90BCC25.jpg %}
- 训练后好的Anto encoder中的NN Decoder其实就可以理解为NN Generator
  - 简单来说就是输入一个vector，然后输出图像
  {% asset_img resources/B8B0E5D6B03F0A4982F7A5E15E425116.jpg %}
- 如果训练集数据比较小，当出现0.5a + 0.5b输入时无法判断
  - a,b都能正确判断，但是当各0.5的时候就不好使了
  - 如下使用VAE解决
{% asset_img resources/A774B365F5C4321FBFA92BAD08736468.jpg %}

#### VAE
{% asset_img resources/77926F12725C356845A4CC5FB5877E9F.jpg %}
- 如何更好的判断input img和output img相似，如何取舍？
  - 比如我们不能简单的用pixel不同个数来决定，如下图明显6 pixel error更好
  {% asset_img resources/BD30A4E204F8A4C2BE6EC062FFB978D1.jpg %}

#### 单纯的learn Generator困难的地方(Auto encoder可能遇见问题)
- 邻近的component无法交流
  - 当然如果考虑更加深的NN，多加入些隐层，可能能够解决
{% asset_img resources/022A31BA195753A93ABF96710BEAAB8D.jpg %}
- 例如:
  - 绿色为GAN得到的结论
  - 蓝色为Auto-encoder(单纯Generator)得到的
  - 原因是无法得到邻近的component的关系
{% asset_img resources/BA81A7B818E0D517E2047D745B53AE9A.jpg %}


###  Can Discriminator generate
#### Discriminator复习
- Discriminator在不同的领域有不同的名字,evaluation function,potential function....
{% asset_img resources/7D4F8ADAF45151FA632840F0CCF981DB.jpg %}

#### Discriminator容易解决component与component之间的关系
- 如果已经有了整张图片，来判断图片是否ok，比较好处理
  - 比如下图中，Discriminator就是一个CNN，这个CNN中有一个检测是否有独立的的filter，这样就很容易检测了
{% asset_img resources/251216A45C8730808FACD751AC7F86BB.jpg %}

#### Discriminator擅长批评，不擅长生成
{% asset_img resources/415E152AE13988117AAAA3E8485D1FCD.jpg %}
- need some negative example
  - 所以产生negative example是关键
  {% asset_img resources/E809C331F97DED3FEBF074A9F71546CA.jpg %}
  - 可能需要一个好的程序去产生negative example
    - 并不能随机生成，如果随机生成，那么一些处于中间状态的图片也没有办法处理
    - 而且需要一个Discriminator来判断是否是好的negative example
      - 这样就会有鸡生蛋，蛋生鸡的问题了:我们需要好的negative example来训练Discriminator,同时又需要好的Discriminator来协助产生negative example
  {% asset_img resources/60F6EC38D31F5F0A61D3421E4955FF57.jpg %}
- Iteration方法解决
  - Discriminator Training
    - 只要能够找到$\tilde x=arg maxD(x)$,Discriminator就能够自己train，而不需要Generator
    - 用iteration方法，不断用此轮迭代得到的D去产生negative example(用D去找出自己的弱点)，然后迭代训练
  {% asset_img resources/5323A485E0B3877A1816C4156D24F3EB.jpg %}
  - Discriminator Training实际类似曲线图
    - 第一张图中，可以看见在没有sample区域，D(x)也可能判断出很高的分数
    - 第二张图中，用得到的D(x)生成新的negative example,然后再从新调整D(x)(让随机产生的negative example分数低)
    - 第三张图中，就是第二轮训练后的曲线图
      - 直观感觉：总体说就是用D(x)去生成非real example区域的高分negative example，然后不断调整自己，让其分数变低
  {% asset_img resources/3BC158DF7FB206E0ABB9BD3EC8417C9A.jpg %}
- Graphical model
  - 其实就是用的Discriminator方法，在ml中其他的structure modle其实方法也是类似：
    就是有一些negative和positive的样本，然后产生一个model，然后用此model再生成些negative example,然后再train....
{% asset_img resources/8C8C6F5948E5A3311053250CE5B92454.jpg %}

#### Generator与Discriminator
- 优缺点
  - Generator容易生成，但不易判断组件与组件直接的关系
  - Discriminator有全局观，但不易做生成
  {% asset_img resources/FF1C2EB528B230870F7CE2971B5F749E.jpg %}
- 组合
  - Generator就替代了arg maxD(x),解决了Discriminator很难做生成的问题
  {% asset_img resources/E74CAFA5583574E1C5FD53E60141C4A4.jpg %}
  {% asset_img resources/6B95811287B3A08F948052B44AA87F83.jpg %}
- VAE & GAN
  {% asset_img resources/5BAAC471C2277A43B3B60F12485B0C76.jpg %}
  - VAE比较稳，但最终的效果还是没有GAN好
  - 各种GAN其实效果没有太大区别
  {% asset_img resources/D8BB36BDDE1EA4D6A5AB28EC2203807B.jpg %}