---
title: SVM
date: 2018-06-28 22:40:02
tags: 
- 机器学习
- 监督学习
- 分类模型
categories: 
-  机器学习
-  监督学习
---

#### 间隔与支持向量
- 支持向量：假设超平面(w,b)能将训练样本正确分类，即对于$(x_i,y_i)\in D$,若$y_i=1$,则有$w^Tx_i+b>0$;若$y_i=-1$,则有$w^Tx_i+b<0$令
  {% asset_img resources/C545E3E754ED9E6EB32618677B8AC1D9.jpg %}
  如图 6.2所示，距离超平面<font color='blue'>最近的这几个训练样本点</font>使式(6.3)的等号成立， 它们被称为"支持向量" (support vector)
  - svm是解决分类问题，但并不会计算所以的点,只计算支持向量
- 间隔：两个异类支持向量到超平面的距离之和被称为"间隔" (margin).
  {% asset_img resources/B70FA52BD036240F08C998533D4D83A3.jpg %}

#### 硬间距与软间距
- 硬间距:前面介绍的支持向量机形式是要求所有样本均满足约束(6.3)， 即所有样本都必须划分i丘确
- 软间距：软间隔则是允许某些样本不满足约束6.3，允许某些样本不满足:$y_i(w^Tx_i+b)>=1$
  - 在最大化间隔的同时，不满足约束的样本应尽可能少
  {% asset_img resources/F0A10EEADFE86AFD179E763B252B3431.jpg %}
  - 松弛变量($\varepsilon$)与软间隔支持向量机
  {% asset_img resources/8279825E046F44EC977D442FDD7C903D.jpg %}
- <font color='blue'>优化目标：</font>
  {% asset_img resources/938EF5CF0044A14AC045C146CD2399F8.jpg %}

#### 替代损失
- $l_{0/1}$非凸、非连续，数学性质不太好，使得式 (6.29)不易直接求解.于 是，人们通常用其他一些函数来代替$l_{0/1}$
- 三种常用的替代损失函数:
  {% asset_img resources/5C561540C4679CEBFF77440F59C5DBEA.jpg %}
  {% asset_img resources/67CA8735991FC2CCCEA06FDDE4F2EC87.jpg %}
  {% asset_img resources/D2C130615908A44229AA9B8C067B4A80.jpg %}

#### 约束求解&拉格朗日乘子求解&KKT
- 求解最优问题：1.无约束（一般直接求导）2.等式约束（用拉格朗日求解）3.不等式约束（用KKT求解）
- 拉格朗日乘子：通过拉格朗日这位大神的办法重新定义一个无约束问题（大家都喜欢无拘无束），这个无约束问题等价于原来的约束优化问题，从而将约束问题无约束化
- 式(6.35)中每个样本都有一个对应的松弛变量， 用 以表征该样本不满 足约束(6.28)的程度.但是，与式(6.6)相似，这仍是一个二次规划问题于是?类 似式 (6.8)，通过拉格朗日乘子法可得到式 (6.35)的拉格朗日函 数
  {% asset_img resources/8B3EC6F476E13888172948983D4EAE13.jpg %}

#### 核函数
{% asset_img resources/23FCF88EDB0FDB93728C5A2548BE7E14.jpg %}

#### 支持向量回归
传统回归模型通常直接基于模型输出 f(x) 与真实输出y之 间的差别来计算损失，当且仅当 f(x) 与 y 完全相同时，损失才为零.与此不同， 支持向量回归 (Support Vector Regression，简称 SVR)假设我们能容忍 f(x) 与 y 之间最多有$\epsilon$的偏差，即仅当 f(x) 与 u 之间的差别绝对值大于 E 时才计算损 失.如国 6.7所示?这相当于以 f(x) 为中心?构建了一个宽度为$2\epsilon$的问隔带，若 训练样本落入此间隔带，则认为是被预测正确的.
{% asset_img resources/127FBB39702127EC06BEE121F38B013B.jpg %}