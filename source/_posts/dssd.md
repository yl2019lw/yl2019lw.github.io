---
title: DSSD:Deconvolutional Single Shot Detector
tags:
  - arxiv
  - detection
category:
  - computer vision
  - detection
  - one-stage detector
date: 2019-06-27 16:50:48
---

# 介绍

文章提出DSSD，其将Deconvolution融入SSD获得更佳性能，基础网络也从Vgg16切换为ResNet-101。

相较于SSD在基础网络后增加几个递减feature map的卷积层, DSSD额外增加一系列转置卷积并结合skip connection可更好预测小目标。

{% asset_img dssd.png DSSD %}

在feature map的每个位置基于一系列default box预测类别score及位置regression，有以下变种。

{% asset_img prediction.png Prediction Module %}

Deconvolution模块通过skip connection融合不同大小的feature map，其实现如下所示。

{% asset_img deconvolution.png Deconvolution Module %}

# 参考文献

{% blockquote %}

Fu, C. Y., Liu, W., Ranga, A., Tyagi, A., & Berg, A. C. (2017). Dssd: Deconvolutional single shot detector. arXiv preprint arXiv:1701.06659.

{% endblockquote %}
