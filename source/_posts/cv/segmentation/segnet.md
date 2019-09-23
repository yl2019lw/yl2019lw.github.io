---
title: SegNet:A Deep Convolutional Encoder-Decoder Architecture for Image Segmentation
tags:
  - TPAMI2017
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-06-05 10:34:55
---

# Introduction

文章提出了SegNet进行语义分割，包括encoder和decoder两部分。Encoder采用VGG16前面13层卷积，Decoder为与Encoder对称的13层网络，不同之处在于最终层使用softmax进行逐像素分类。

{% asset_img segnet.png segnet %}

Decoder网络将feature map还原至原输入大小进行分类，其采用Encoder pooling过程中相应的pooling indices进行上采样。无学习参数的unpooling上采样结果为稀疏特征图，再通过卷积层产生稠密feature map。通过此实现方式，SegNet有较少的内存占用和计算资源消耗。

SegNet与FCN Decoder对比部分见下图：

{% asset_img decoder.png segnet decoder %}

# 参考文献

{% blockquote %}

Badrinarayanan, V., Kendall, A., & Cipolla, R. (2017). Segnet: A deep convolutional encoder-decoder architecture for image segmentation. IEEE transactions on pattern analysis and machine intelligence, 39(12), 2481-2495.

{% endblockquote %}
