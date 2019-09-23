---
title: R-FCN:Object Detection via Region-based Fully Convolutional Networks
tags:
  - NIPS2016
  - detection
category:
  - computer vision
  - detection
  - two-stage detector
date: 2019-06-27 11:42:12
mathjax: true
---

# 介绍

文章提出R-FCN使用全卷积网络进行目标检测。通常两阶段检测器中RoI pooling操作后续分类及bounding box回归是针对每个RoI单独计算，使用全卷积方式可基于整张图片共享计算。

文章使用了position sensitve score map来解决分类问题需要的translation-invariant与检测问题需要的translation-variant冲突。

R-FCN为每个类别生成$k^2$个score map而非一个，每个score map对应物体的一个相对位置，如k=3时有对应上左，上中，上右...下右等9个相对位置。

同InstanceFCN一样, 使用位置敏感score map可使重叠的物体使用不同的score map，因其在重叠某处必然拥有相对各自物体不同的相对位置。

{% asset_img psm.png Position sensitive score map %}

R-FCN在保持与Faster R-CNN相近的性能时大幅提升了速度。

# R-FCN

类似于Faster R-CNN，R-FCN也首先通过RPN网络生成region proposal，然后从feature map相应位置执行Position sensitive RoI pooling进行object分类与box回归。

位置敏感RoI pooling过程类似于InstanceFCN中score map合并过程，对于$k \times k$大小的格子均只使用物体相对位置处的那一个格子内部的内容。

如上图所示, 其object分类分支生成$k^2(C+1)$张score map，执行位置敏感RoI pooling后得到$k \times k \times (C+1)$张量，进行spatial平均后使用softmax完成多分类。

其box回归分支生成类别无关的$4k^2$张score map，通过平均投票后得到4维向量来表征中心点、宽、高的偏差。

{% asset_img r-fcn.png R-FCN %}

# 参考文献

{% blockquote %}

Dai, J., Li, Y., He, K., & Sun, J. (2016). R-fcn: Object detection via region-based fully convolutional networks. Advances in neural information processing systems, 379-387.

{% endblockquote %}
