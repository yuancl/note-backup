---
title: KMeans
date: 2018-07-10 21:09:02
tags: 
- 机器学习
categories: 
-  机器学习
---

#### 算法步骤：
- 随机选择K个簇中心点$\mu_1,\mu_2,\mu_3...\mu_k$
- 然后依次加入其它节点，采用距离最近(该节点到各簇中心距离)的判别方式
- 重新计算各簇中心点(因为有新节点加入)，直到所有节点都已加入
  {% asset_img resources/B02EB1114F53182AC4C8A33C041F5A71.jpg %}

#### Random initialization
- 会重复KMeans n次，找出cost最小的k值
{% asset_img resources/D1E2FCC1B588D77F14AA8C949B1A487E.jpg %}
{% asset_img resources/32DA03A8B43321EEE51CE601BF8B214B.jpg %}
#### Cost funciton
所有点到各簇中心点距离和
{% asset_img resources/BC93652FCD6FE37D162DE72468A473F6.jpg %}