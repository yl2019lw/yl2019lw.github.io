---
title: VDSR:Accurate Image Super-Resolution  Using Very Deep Convolutional Networks
tags:
  - CVPR2016
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-05 21:44:08
mathjax: true
---

# 介绍

文章提出VDSR，以更深的网络获得更好的super-resolution结果。文章仅学习残差图像，使用自适应梯度裁剪允许使用更大的学习率来加快收敛，同时仅使用单一网络对多个尺度的图像进行高分辨率还原。

# VDSR

文章堆叠一系列$3 \times 3$大小的卷积核，共20层，感受野为$41 \times 41$。一张高分辨率图像可分解为低频部分与高频部分，低频部分对应低分辨率图像，高频部分对应图像细节信息。在超分辨率任务中，低分辨率输入与高分辨率输出二者共享图像的低频部分，仅需要针对残差图像部分进行学习。

{% asset_img vdsr.png vdsr %}

通常gradient clipping限制梯度在一个范围$[-\theta, \theta]$，如果使用较大的学习率则容易出现梯度爆炸问题，需要将$\theta$设置的较小。使用学习率衰减策略后，过小的步长会减缓收敛，文章将梯度限定在$[-\frac{\theta}{r}, \frac{\theta}{r}]$，其中$r$为当前的学习率。

# Metric
在PSNR之外，也使用了SSIM指标来衡量重构后的图像与原始高清图像的结构相似性。

{% asset_img ssim.png ssim %}

{% asset_img ssim_extra.png ssim_extra %}

# 参考文献
{% blockquote %}

Kim, J., Kwon Lee, J., & Mu Lee, K. (2016). Accurate image super-resolution using very deep convolutional networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1646-1654).
<br/>
https://zh.wikipedia.org/zh-cn/結構相似性

{% endblockquote %}
