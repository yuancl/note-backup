---
title: NeuralNetwork
date: 2018-06-18 07:41:11
tags: 
- 机器学习
- 机器学习基础
categories: 
-  机器学习
-  机器学习基础
---

[习题代码:NeuralNetwork](https://github.com/yuancl/ml-algorithm/tree/master/exe4-NeuralNetwork)

#### 人脑神经元
- 人脑内就有很多神经元构成，神经元与神经元之间互相连接，传递电信号等
  {% asset_img resources/A304F5025E48BA4324FCD8DB562EBBC1.jpg %}

#### 模型
- 基础结构：类似逻辑回归模型
  {% asset_img resources/A155266DFD2672B1850698451F99A310.jpg %}
- 网络架构
  - 每个神经单元都是一个上图的基础结构，输出a是激活值,g是sigmoid激活函数
  - 架构分为第一层(输入层)，第二层(隐藏层)....输出层
  {% asset_img resources/445D829784D90FD50DB465E3F95031CC.jpg %}
  - 前向计算：每个神经元都依次计算
  {% asset_img resources/DF4A1399852DE9F18094C55A08479F78.jpg %}

- 直观理解网络含义
  - 每一层都是通过逻辑回归来完成，通过增加隐层的数量，或者通过增加一个隐藏中的单元数，来完成不同的功能，就能够得到更复杂的模型，完成更复杂的功能<font color='red'>（简单理解为每个神经单位都是一个逻辑回归结构，都能够完成一个独立的功能）</font>
    - 比如亦或问题
      {% asset_img resources/6B39D24F15F400C523F22D22183EC541.jpg %}

#### 处理多分类问题
- 每个分类被一个节点判断出(像上一节类似，一个节点判断and，一个节点判断or，它们的下一层节点就能判断更复杂的内容)
- 多个分类，可以在一个神经网络中判断输出
- 逻辑回归时候，是把多分类问题，视为多个二分类问题处理的(多个$h_\theta^i(x)$)
{% asset_img resources/3DB7E197A148553098F55835C55544B9.jpg %}

#### Cost function
- 和逻辑回归的cost function非常类似，其中m表示样本数，K表示神经网络最终输出的个数，正则化项包含了所有参数
- {% asset_img resources/B14FBC4B8353C514D5B36255A966E074.jpg %}

#### 求最优解
- 求解算法最常用的当然也是使用的梯度下降，然后也可以使用深度学习中的[动态梯度下降,RMProp,Adam](https://yuancl.github.io/2018/09/10/dl/%E7%AC%AC%E4%BA%8C%E9%97%A8%E8%AF%BE-%E7%AC%AC%E4%BA%8C%E5%91%A8/)等
- 梯度的计算也可以使用最原始的方法，对每个参数求解梯度，但是由于神经网络中参数太多，原始方法效率太低，所以有了后面的[Backpropagation算法](https://yuancl.github.io/2018/06/20/ml/Backpropagation/)