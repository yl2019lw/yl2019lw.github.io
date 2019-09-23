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

Deeplab采用vgg16作为基础网络结构，首先采用卷积网络获得分割score map，再通过dense CRF调整输出结果。

{% asset_img deeplab.png deeplab %}

## atrous convolution

Deeplab引入孔洞卷积，相当于处理卷积输入时在孔洞处参数为0，在有效扩大感受野的同时不增加额外的参数。常规卷积操作时input stride为1，即使用的相邻位置的数据作为输入，而孔洞卷积相当于对输入数据进行有间隔的采样。

{% asset_img hole.png atrous %}

## Dense CRF

Deeplab使用密集条件随机场对全卷积网络输出结果进行refinement，Dense即将任意两个输入位置作为结点进行联接，相应能量函数如下：

\begin{equation}
    E_(x) = \sum_{i}\theta_i(x_i) + \sum_{ij}\theta_{ij}(x_i, x_j)
\end{equation}
其中$x$为预测像素label，由分类概率确定一元能量函数$\theta_i$，任意二个像素间定义的二元能量函数为:

\begin{equation}
    \theta_{ij}(x_i, x_j) = u(x_i, x_j)\sum_{m=1}^{K} w_m \cdot k^m(f_i, f_j)
\end{equation}
当$x_i = x_j$时$u_{x_i,x_j} = 1$，否则为0。$k^m$为一系列依赖像素点位置与亮度关系的高斯核函数，定义如下：

\begin{equation}
    w_1 exp(- \frac{||p_i - p_j||^2} {2\sigma_{\alpha}^2} - \frac{||I_i - I_j||^2} {2\sigma_{\beta}^2}) + w_2 exp(- \frac{||p_i - p_j||^2} {2\sigma_{\gamma}^2}) 
\end{equation}

Dense CRF可使用mean field approximation高效计算。

# 参考文献

{% blockquote %}

Chen, L. C., Papandreou, G., Kokkinos, I., Murphy, K., & Yuille, A. L. (2014). Semantic image segmentation with deep convolutional nets and fully connected crfs. arXiv preprint arXiv:1412.7062.

{% endblockquote %}
