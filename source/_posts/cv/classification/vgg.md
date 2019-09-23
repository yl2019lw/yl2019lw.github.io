---
title: VGG:Very deep convolutional networks for large-scale image recognition
date: 2019-03-19 14:25:31
tags:
  - ICLR2015
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍

VGG模型来自牛津大学Visual Geometry Group, 其堆叠一系列3x3卷积核，在ILSVRC-2014获得定位任务第一名，分类任务第二名。

# 架构

模型仅使用3x3及1x1大小的卷积核，3x3为可捕获上下左右信息的最小卷积窗口。所有的3x3卷积有padding=1，步长为1，因此输入输出的特征图大小不变。

池化层窗口大小为2x2,步长为2，每次池化后特征图大小减半。紧接池化后的卷积通道数翻倍，直至最后为512，保持运算的均衡。

两层连续堆叠的3x3卷积与直接5x5卷积有大小相同的感受野，三层连续堆叠的3x3卷积与直接7x7卷积有大小相同的感受野，因此可使用堆叠的较小卷积核来替代较大的卷积。

* 堆叠的小卷积核相较于单层大卷积核有更强的判别能力。

    因堆叠后层数更深，卷积之间有更多的非线性变换(relu), 可增加模型的判别能力。

* 堆叠的小卷积核相较于单层大卷积核有更少的参数。

    如3个3x3通道为C的卷积核参数量为$3(3^2C^2)=27C^2$, 而1个7x7通道为C的卷积核参数量为$7^2C^2=49C^2$。

不同深度的模型配置如下表：

{% asset_img vgg.png This is VGG architecture %}

# 实验

* 多尺度训练

* 多模型集成

# 参考文献

{% blockquote %}

Simonyan, K., & Zisserman, A. (2014). Very deep convolutional networks for large-scale image recognition. arXiv preprint arXiv:1409.1556.

{% endblockquote %}
