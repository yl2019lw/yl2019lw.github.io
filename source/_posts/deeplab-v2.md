---
title: DeepLab:Semantic Image Segmentation with Deep Convolutional Nets, Atrous Convolution, and Fully Connected CRFs
tags:
  - TPAMI2017
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-06-04 11:34:00
mathjax: true
---

# Introduction

文章为DeepLab系列第二个版本，仍然结合带孔洞的卷积网络与密集条件随机场，相对Deeplab V1主要增加了atrous spatial pyramid pooling部分来综合利用多个尺度的信息。

# Atrous Convolution

Deeplab使用孔洞卷积，对输入数据使用步长rate进行间隔采样，如下图所示:

{% img /deeplab-v2/hole.png 500 atrous %}

则1-D卷积输入输出对应关系可表示如下：

\begin{equation}
    y[i] = \sum_{k=1}^{K}x[i + r \cdot k]w[k]
\end{equation}

Atrous convolution使用rate为r时相当于在相邻卷积参数中插入了$r-1$个0，对于$k \times k$大小的孔洞卷积其实际有效感受野大小为$k_e = k + (k-1)(r-1)$。

# Atrous Spatial Pyramid Pooling

{% img /deeplab-v2/aspp.png 500 atrous spatial pyramid pooling %}

文章采用多个不同rate的孔洞卷积来利用多尺度信息,并将其输出结果进行融合。

{% img /deeplab-v2/deeplab-aspp.png 500 deeplab-aspp %}


# 参考文献

{% blockquote %}

Chen, L. C., Papandreou, G., Kokkinos, I., Murphy, K., & Yuille, A. L. (2017). Deeplab: Semantic image segmentation with deep convolutional nets, atrous convolution, and fully connected crfs. IEEE transactions on pattern analysis and machine intelligence, 40(4), 834-848.

{% endblockquote %}
