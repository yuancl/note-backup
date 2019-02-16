---
title: Conditional GAN
date: 2019-01-16 07:10:10
tags: 
- 机器学习
- GAN
categories: 
-  机器学习
-  GAN
---

### Conditional GAN介绍
#### 背景
- Traditional supervised approach
  - 正面火车和侧面火车都是好的结果，这会导致最后图片很模糊，因为是多张图片的平均
{% asset_img resources/B506E2E85727105810C3FD98DE358329.jpg %}

- GAN
  - G其实可以很容易忽略D,从而可以无视G的输入(这里是c:train)
    - 比如如果G发现每次输出猫都能得到高分，那么不管G的输入，只要我我每次都输出是清晰的猫就好了
    - 输出低分基本上有下面三种场景
  {% asset_img resources/06288B8EFB718B40410ACFE58F20E136.jpg %}
  - 后面的Conditional GAN可以解决这个问题


#### Conditional GAN
- 输入
  - G的输出x：图片
  - G的输入c：train
- 目标
  - x is realistic or not
  - c ans x are matched or not
{% asset_img resources/71EAA38F9930A85049893063F5F499DD.jpg %}
- 演算法
  - 核心思想：找到Loss(上面三种情况集合)，然后用梯度提升方法去优化
  {% asset_img resources/0FC60DD9F76548EDD7675B99F770ED9E.jpg %}

#### Contitional GAN(D网络架构)
- 分别将x是否是真实图片和c and x是否match分别输出会比较好一点
{% asset_img resources/3EC8A1DCCF4EDAA9523E2F085EF824F0.jpg %}


### Stack GAN
- 思想就是先产生小图，然后产生大图
{% asset_img resources/C1400E303DB9A50B6CC45300D159FEB4.jpg %}


### Image to Image
#### 传统方法(Supervised Learning)
- 也是一样，最后输出会很模糊
  - 因为一个input对应了多个好图（多张图片），就会把这些图片做平均来输出
{% asset_img resources/9A949394AE46F6D8145151F92CE0611D.jpg %}

#### GAN方法
- testing中的close图片，就是supervised learning的结果，比较模糊
- GAN比较清晰了，但是会产生一些其他的内容
- GAN+close，再加入一些限制条件，就是让G产生的图片更接近真实图片
{% asset_img resources/9EAD3C30D5E435ED7070F5B291B3639A.jpg %}

#### Patch GAN
- 图像太大，容易出现各种问题
{% asset_img resources/3AE30BDB0CD1A217FCFFB482DB274D10.jpg %}


### GAN用到其他场景
#### Speech去杂音
- speech生谱图就当做图片，也使用Conditional GAN思想
{% asset_img resources/C11F3C210E44F4A4E9CE5C96C89C7CFF.jpg %}

#### Video
- 判断是否是正确的连续的影片
  - 将影片一张张剪辑，形成图片，然后进行训练
{% asset_img resources/E9F52BFDB4677AE62051EA82760CADC3.jpg %}