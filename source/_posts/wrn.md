---
title: Wide Residual Networks
date: 2019-03-23 12:57:10
tags:
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍

文章提出了通过扩展神经网络的宽度而不是深度也可以获得良好的性能，同时验证了残差单元中使用dropout的有效性。对于卷积神经网络而言，宽度指的是在每一层中使用的卷积核的数目，即通道这一维度的数目。

文章使用Resnet作为基本的参考网络结构，为了提高深度模型的表示能力，可以在每个残差单元中加入更多的层，扩大卷积层中卷积核的大小或扩宽卷积核。

文章使用以下符号标记：

* l: deepening factor，每个残差单元内卷积层的数量，对于basic版本Resnet是2，bottleneck版本是3。

* k: widdening factor，每个卷积层处卷积核的数目是原始的多少倍，原始的网络结构都是1。

* d: number of blocks, 基本残差单元的数量, 网络正是由一系列基本的残差单元堆叠而成。

* B(M): B表示一个残差单元，M为此残差单元内的一系列卷积层，卷积核都是方块，残差单元内的卷积层都有相同的通道数。如B(3,3)就是原始basic版本Resnet残差单元，B(1,3,1)是对原始bottleneck版本Resnet残差单元的一个加强。(bottneck先1x1降维后1x1还原维度)

* n: 网络中全部卷积层的数量。WRN-n-k表示一共有n层，widdening factor为k。

文章通过实验发现网络层有相近数量的参数时模型输出有相近的性能。在网络有不同的深度时加宽网络能连续性地提升性能，同时加深网络和加宽网络会带来更多的参数进而需要更多的数据来防止拟合，同时在残差单元内的卷积层之间使用dropout是有效的，尽管已经使用了Batch Normalization。文章显示了使用16层加宽的网络能够获得媲美1000层的未加宽的性能，同时能够加快训练速度。


# 参考文献
{% blockquote %}

Zagoruyko, S., & Komodakis, N. (2016). Wide residual networks. arXiv preprint arXiv:1605.07146.

{% endblockquote %}

