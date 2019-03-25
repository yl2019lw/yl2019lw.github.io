---
title: Inceptionv3:Rethinking the inception architecture for computer vision
date: 2019-03-25 11:41:46
tags:
  - CVPR2016
  - classification
category:
  - computer vision
  - classification
---

# 介绍
文章旨在通过合理地设计Inception结构达到更有效地利用增加的计算来获得更好地性能，在ILSVRC分类任务上获得17.3%的top-1 error及3.5% top-5 error。

# General Design Principles
为有效扩展卷积网络地表示能力，文章利用Inception模块中存在的dimension reduction和parallel structures特性，给出了几个通用的设计原则：

* 避免表示瓶颈，尤其在网络的初始层。表示维度应该逐步地从输入递减到输出。

* 高维特征表达通常有更强的区分能力，带来更快的训练速度。

* 空间聚合在低维表示处进行将不会有太多的表示能力损失。

* 需要平衡网络的深度与宽度。

# Factorizing Convolutions

* 分解为小的卷积

大的卷积核可以分解为多个小卷积的堆叠，如5x5卷积与两个堆叠3x3卷积有相同的感受野, 但使用小的卷积核有更少的参数量同时又不损失表示能力。

{% img /2019/03/25/inception-v3/5_5_factor.png 400 5x5 factor %} 

可将原始Inception结构中使用的5x5卷积替换为堆叠的两个3x3卷积。

{% img /2019/03/25/inception-v3/inception_5_5.png 400 inception replace 5x5 %}

* 分解为非对称的卷积

相较于分解为一系列3x3卷积，分解为非对称的卷积会有更少的参数量。如3x3卷积可分解为3x1卷积后接1x3卷积。

{% img /2019/03/25/inception-v3/3_3_factor.png 400 3x3 factor %} 

n x n卷积可分解为1 x n卷积后接n x 1卷积。

{% img /2019/03/25/inception-v3/n_n_factor.png 400 3x3 factor %} 

# Efficient Grid Size Reduction

通常通过1x1卷积调整特征通道维度，通过池化减小feature map，如输入为$d \times d \times k$，输出为$\frac{d}{2} \times \frac{d}{2} \times 2k$可组合1x1卷积和池化完成。如先池化再卷积比先卷积再池化虽然有更少的参数，但会损失模型表示能力：

{% img /2019/03/25/inception-v3/alternative.png 400 alternative %}

实际上可以通过以下方式来更好地解决表示瓶颈，同时也拥有较少的参数：

{% img /2019/03/25/inception-v3/grid_size.png 400 reduce grid size %}

# Inception-V3

Inception V3结构如下表：

{% img /2019/03/25/inception-v3/inception_v3.png 400 3x3 factor %} 


# 参考文献
{% blockquote %}

Szegedy, C., Vanhoucke, V., Ioffe, S., Shlens, J., & Wojna, Z. (2016). Rethinking the inception architecture for computer vision. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 2818-2826).

{% endblockquote %}
