---
title: Inception-v4,Inception-ResNet and the Impact of Residual Connections on Learning
date: 2019-03-25 21:09:57
tags:
  - AAAI 2017
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍

文章提出了更复杂的Inception结构，并与ResNet进行了结合，最终集成模型获得ImageNet分类任务top-5 error为3.08%。

# Inception-V4

Inception-V4结构如下：

{% img /inception-v4/inception-v4.png 500 inception v4 %}

其中对应Inception-A,Inception-B,Inception-C如下：
{% asset_img inception-v4-schema.png inception v4 %}

# Inception-ResNet

Inception-ResNet结构如下：
{% img /inception-v4/inception-resnet.png 500 inception resnet %}

其中对应Inception-ResNet-V1版本Inception-A,Inception-B,Inception-C如下：
{% asset_img inception-resnet-v1-schema.png inception resnet v1 %}

其中对应Inceptipn-ResNet-V2版本Inception-A,Inception-B,Inception-C如下：
{% asset_img inception-resnet-v2-schema.png inception resnet v2 %}

# Result

不同版本Inception网络在ImageNet运行结果如下：
{% img /inception-v4/result.png 500 result for different versions of inception network %}

# 参考文献

{% blockquote %}

Szegedy, C., Ioffe, S., Vanhoucke, V., & Alemi, A. A. (2017, February). Inception-v4, inception-resnet and the impact of residual connections on learning. In Thirty-First AAAI Conference on Artificial Intelligence.

{% endblockquote %}
