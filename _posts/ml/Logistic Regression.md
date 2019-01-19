---
title: Logistic Regression
date: 2018-06-08 19:40:01
tags: 
- 机器学习
categories: 
-  机器学习
---
[习题代码:Logistic Regression](https://github.com/yuancl/ml-algorithm/tree/master/exe2-LogisticRegression)
#### 使用场景：解决分类问题
- Logistic Regression使用场景是解决分类问题
  - 二分类或者多分类,多分类模型(多次二分类或者后面学习到的softmax)
  - 数据分布
    - 二分类模型中，线性分布：
      {% asset_img resources/C491A76AE474D3A6527DE11CE6B5BB48.jpg %}
    - 也可能是非线性分布：
      {% asset_img resources/34441106EC0A9630E1BA17393165253B.jpg %}
      
#### 逻辑回归模型
- Hypothese：$h_\theta(x)=g(z)=\frac{1}{1+e^{-z}}$
  Sigmoid:
  {% asset_img resources/9DEA3236A7F3E3EE56B5E39F09094DFE.jpg %}
  所以：$y = \begin{bmatrix} 
  1(h_\theta(x) >= 0.5)
  \\ 
  0(h_\theta(x) < 0.5)
  \end{bmatrix}$
- $z = \begin{bmatrix} 
  \theta^Tx = \theta_0+\theta_1x_1+\theta_2x_2...(数据线性分布)
  \\ 
  x^2+y^2-1 (非线性分布，这里是圆形分布)
  \end{bmatrix}$
- Cost fun:
  {% asset_img resources/51E29FDE06731C99AB2EAD9F6BFC5705.jpg %}
  - 推导：
    {% asset_img resources/A33831535408D08F4745CEC4917E73A4.jpg %}
    {% asset_img resources/3B4C93B55A4452695CDA033B1F77DA1A.jpg %}
- 梯度：
  {% asset_img resources/6CCB2EBFDDAC0EEC7FE859C459F82619.jpg %}

#### 条件概率角度理解
- $h_\theta(x) = P(y=1|x;\theta) = 1-P(y=0|x;\theta)$
- $P(y=1|x;\theta) + P(y=0|x;\theta) = 1$

#### 正则化
- 当数据分布为非线性的时候，如果使用多项式去拟合，那么很容易出现过拟合问题，这个时候就需要使用L1，L2正则化去处理
- 直观理解：正则化的根本目的就是将过拟合的参数调到合适大小
  {% asset_img resources/2A2FB337EA2AB08BF7E63A23C3F2FC15.jpg %}
- cost_reg:
  {% asset_img resources/FA37074C59A156386726A45623DE0E27.jpg %}
- 梯度_reg:
  {% asset_img resources/72EA1437BA2B8A620AA0A95846B077B8.jpg %}
- 超参数$\lambda$对拟合程度和准确率等的影响
  {% asset_img resources/4749D216F6C432BF5A66591AAEA97A13.jpg %}

#### 多分类
- 思想：对每个类别都会有一个$h_\theta(x)$对应：$h_\theta^i(x)$。输入x，获取使最大的$h_\theta^i(x)$的i值
  {% asset_img resources/201EA81A2D0FD9F6D4EB13CD721C24E7.jpg %}