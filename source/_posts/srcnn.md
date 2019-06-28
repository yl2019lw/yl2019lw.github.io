---
title: SRCNN:Learning Deep Convolutional Network for Image Super-Resolution
tags:
  - ECCV2014
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-05 16:36:36
mathjax: true
---

# 介绍

文章提出SRCNN使用深度学习模型解决single image super-resolution问题，网络可对从低分辨率的图片到高分辨率的图片的映射关系进行端到端地学习，并给出了相对于sparse-conding方法的可解释性。

# SRCNN

## bicubic interpolation

低分辨率图片需要进行预处理，即通过双三次插值放大到指定大小后进行训练。双三次插值即在二维方向各进行三次多项式插值，共需要周围16个的点的信息，示意图如下。

<div class='img-size-half'>
{% asset_img bicubic_interpolation.png bicubic interpolation %}
</div>

## formulation

SRCNN为三层卷积网络结构，包含patch抽取及表示、非线性变换、重构，网络示意图如下：

{% asset_img srcnn.png srcnn %}

第一层使用$f1 \times f1$大小卷积将局部patch表示为一高维特征向量。为保持与以前的方法一致，仅使用luminance通道进行训练(YCbCr)。
第二层进行非线性变换，获得相对应的高分辨率patch表示。第三层将高清patch表示聚合为高清图片。

## relationship to sparse-coding 

基于sparse-coding的方法首先在输入图片上获取特定大小重叠的patch，然后从这些patch训练出低分辨率的词典。对于每个低分率patch的稀疏表示系数作用于高分辨率patch词典，则可获得对应的高分辨率的patch
，将高分辨率patch的结果聚合在一起可得高分辨率图片。可将基于sparse-coding的方法视作卷积网络的对应过程，示意图如下：

{% asset_img sparse-coding.png sparse-coding %}

## loss & metric

对图像I及其噪声近似K，使用像素级均方误差MSE作为损失函数：
\begin{equation}
    MSE(I, K) = \frac{1}{mn}\sum_{i=0}^{m-1}\sum_{j=0}^{n-1}[I(i,j) - K(i,j)]^2
\end{equation}

峰值信噪比PSNR为
\begin{equation}
    PSNR = 20 \cdot log_{10}(\frac{MAX_I}{\sqrt{MSE}})
\end{equation}
其中$MAX_I$为图像中最大亮度，如255。

# 参考文献

{% blockquote %}

Dong, C., Loy, C. C., He, K., & Tang, X. (2014, September). Learning a deep convolutional network for image super-resolution. In European conference on computer vision (pp. 184-199). Springer, Cham.
<br/>
https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio
<br/>
https://en.wikipedia.org/wiki/Bicubic_interpolation

{% endblockquote %}
