---
title: Deeplab v1:Semantic image segmentation with deep convolutional nets and fully connected crfs
tags:
  - ICLR2015
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-05-14 21:43:54
mathjax: true
---

# 介绍

文章提出的Deeplab结合使用了卷积神经网络及全连接条件随机场进行图像语义分割，获得了state-of-the-art性能。

图像分割属于low-level vision task，直接使用深度神经网络存在以下挑战：一是网络中的卷积层stride及pooling操作带来的signal downsampling；二是通常用于分类的卷积网络需要位置无关特性，而细粒度的位置信息对分割任务十分重要。文章使用孔洞卷积解决第一个问题，使用全连接条件随机场解决第二个问题。

# Deeplab

Deeplab采用vgg16作为基础网络结构。

{% asset_img deeplab.png deeplab %}


## atrous convolution

{% asset_img atrous.png atrous %}

## 

# 参考文献

{% blockquote %]

Chen, L. C., Papandreou, G., Kokkinos, I., Murphy, K., & Yuille, A. L. (2014). Semantic image segmentation with deep convolutional nets and fully connected crfs. arXiv preprint arXiv:1412.7062.

{% endblockquote %}
