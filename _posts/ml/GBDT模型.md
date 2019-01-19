---
title: GBDT模型
date: 2018-07-27 07:19:11
tags: 
- 机器学习
categories: 
- 机器学习
---

[集成学习方法](https://yuancl.github.io/2018/07/24/nlp/集成学习方法/)

#### GB(Gradient Boosting)理解
- 理解梯度下降
  - 求解目标函数$J(\theta)$的最优解，本质上是一轮一轮的求解w
  - 对于最终的最后解W*,是由初始值$w_0$经过M次迭代得到的
  
- 对比理解多轮迭代后的GB函数最优解
  - $f(x)$经过M次迭代，得到$F(x)=\sum_{i=1}^m\theta _if_i(x)$
  - 这里是反向思考：因为梯度下降是通过梯度下降的方法求出w参数值，最后就是经过了M轮迭代求出最优解w*.  这里的w是参数，同样的思想对比到函数F(x)上,<font color='blue'>将F(x)视为整体类似于w</font>，最后通过多轮迭代求出F*(x)最优解

- 可以看出上述是一个求解梯度的过程，因此也称为基于梯度的Boost方法(即GB:Gradient Boosting)

- 算法
  - 这里的m是m个机器学习模型，n应该是样本的数量。这里就能够看出，boost方法就是前一轮的结果会影响后一轮
  {% asset_img resources/297ED5E7B40AD7E5192DB0EE79E577F7.jpg %}
  - 一般定义不同的Loss函数，就能得到不同的算法，比如这里用二分类任务中常用的$L(y,F)=log(1+exp(-2yF)), y \in {\{-1,1\}}$,就能得到如下算法：
  {% asset_img resources/DB082CA541217CF13A4A4DC535C9D2F0.jpg %}

#### DT理解
参考[决策树](https://yuancl.github.io/2018/08/06/ml/决策树/)