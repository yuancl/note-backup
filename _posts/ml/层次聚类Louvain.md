---
title: 层次聚类Louvain
date: 2019-05-22 22:01:10
tags: 
- 机器学习
- 机器学习算法
categories: 
-  机器学习
-  算法
---

[聚类总结](https://yuancl.github.io/2018/08/20/ml/聚类/)

#### 模块度
[社区划分的标准--模块度](https://blog.csdn.net/aspirinvagrant/article/details/45577033)
[社区划分的标准--模块度2](https://blog.csdn.net/wangyibo0201/article/details/52048248)
- 概念
  模块度（Modularity）用来衡量一个社区的划分是不是相对比较好的结果。<font color='blue'>一个相对好的结果在社区内部的节点相似度较高，而在社区外部节点的相似度较低</font>
- 定义
  社区内部的总边数和网络中总边数的比例减去一个期望值，该期望值是将网络设定为随机网络时同样的社区分配所形成的社区内部的总边数和网络中总边数的比例的大小
  - 公式一:
  {% asset_img resources/84A5EA4853C12869CF52242DE25C84BE.jpg %}
  - $k_v*k_w$,及随机网络的理解，可以[参考](https://blog.csdn.net/wangyibo0201/article/details/52048248)
  {% asset_img resources/737C25A63A47EC602CB2F93036C708F6.jpg %}
  - 公式二:
  {% asset_img resources/22E09A2D22EB3AB921C861FE8FE9C8F7.jpg %}
  - {% asset_img resources/D4A72022E1512AE4E3F16210BCAE1FF9.jpg %}
  - 举例：
  {% asset_img resources/7D473F48C579B1842C9272A1D813BB8B.jpg %}
    - 第一项是三项合在一起
    
#### Louvain算法
[参考文章](https://www.cnblogs.com/fengfenggirl/p/louvain.html)
- Louvain算法是由底向上的层次聚类算法的一种，目的是优化提升模块度
- 步骤
  - 1.将图中的每个节点看成一个独立的社区，次数社区的数目与节点个数相同；
  - 2.对每个节点i，依次尝试把节点i分配到其每个邻居节点所在的社区，计算分配前与<font color='blue'>分配后的模块度变化ΔQ</font>，并记录ΔQ最大的那个邻居节点，如果maxΔQ>0，则把节点i分配ΔQ最大的那个邻居节点所在的社区，否则保持不变；
  - 3.重复2），<font color='blue'>直到所有节点的所属社区不再变化</font>；
  - 4.对图进行压缩，将所有在同一个社区的节点压缩成一个新节点，<font color='blue'>社区内节点之间的边的权重转化为新节点的环的权重，社区间的边权重转化为新节点间的边权重</font>；
  - 5.重复1）直到整个图的模块度不再发生变化