---
title: Linear Regression
date: 2018-06-01 18:47:09
tags: 
- 机器学习
- 监督学习
- 回归模型
categories: 
-  机器学习
-  监督学习
---

[习题代码:Linear Regression](https://github.com/yuancl/ml-algorithm/tree/master/exe1-LinearRegression)

#### 回归模型：线性回归
- 场景：房价预测，输入输出能够用线性方程拟合，输出为实数
  {% asset_img resources/2016699DF107B3CA5162C84709E51F54.jpg %}
- 核心公式：
  - LR使用的是最小二乘法
    - 均方误差：是参数估计值与参数真值之差的平方的期望值$MSE=\frac{1}{N}\sum_{t=1}^N(observed_t-predicted_t)^2$
    - 最小二乘法：基于均方误差最小化来进行模型求解的方法称为“最小二乘法”
  - 公式：{% asset_img resources/699B964EB740A4F1E1A3A2FA28746C4A.jpg %}
- 公式推导：
  {% asset_img resources/AD4CFF2A230DDE7364B08A0F6A682AF3.jpg %}
- 心得：
  - 理解向量是n个维度(n行)，一列的特殊矩阵
  - X下角标表示第n个特征，上角标m表示第m个样本，可以理解为第m个样本的向量(每个样本都包含n维的特征)
  - 理解矩阵的乘积，向量，替换普通程序思维的for循环等
  - 数据归一化，会收敛得更快，Loss曲线也更平滑