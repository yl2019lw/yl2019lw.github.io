---
title: DRCN:Deeply-Recursive Convolutional Network for Image Super-Resolution
tags:
  - CVPR2016
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-06 12:33:12
mathjax: true
---

# 介绍

文章提出了DRCN来进行超分辨率还原，综合了recursive-supervision和skip-connection，在加深网络的同时不引入额外参数，获得了相较于之前方法的最佳性能。模型包含Embedding network，Inference network，Reconstruction network三部分，整体架构图如下所示：

{% asset_img architecture.png architecture %}

# DRCN

## Basic Model

模型由低分辨率插值图片$x$作为输入，预测对应高分辨率输出，可表示为$f(x) = f_3(f_2(f_1(x)))$。

### Embedding network

Embedding网络$f1$将输入的低分辨率插值图片转换为特征表示，其包含两层卷积.
\begin{equation}
    H_{-1} = max(0, W_{-1} * x + b_{-1}) \\
    H_{0} = max(0, W_{0} * H_{-1} + b_{0}) \\
    f_1(x) = H_0,
\end{equation}

### Inference network

Inference网络$f2$将$H_0$作为输入，计算其输出$H_D$。Inference网络为recursive，即不同深度的层共享参数。令$g(H) = max(0, W*H+b)$为递归中的一层，则有
\begin{equation}
    H_d = g(H_{d-1}) = max(0, W * H_{d-1} + b) \\
    f_2(H) = (g \circ g \circ \cdots \circ)g(H) = g^D(H)
\end{equation}

### Resconstruction network

Reconstruction网络$f3$将$H_D$作为输入，输出预测的高分辨率图片。
\begin{equation}
    H_{D+1} = max(0, W_{D+1} * H_D + b_{D+1}) \\
    \hat{y} = max(0, W_{D+2} * H_{D+1} + b_{D+2}) \\
    f_3(H) = \hat{y}
\end{equation}

## Advance Model

DRCN使用了Recursive-Supervision与Skip-Connection, 示意图如下：

{% asset_img drcn.png drcn %}

### Recursive-Supervision

首先从Embedding网络提取基本特征后，对Inference网络中不同recursive深度的输出送至同一Reconstruction网络构建相应的高分辨率预测，这样Inference网络有D层就对应D个预测，所有D个不同深度的预测均参与训练。同时可对这D个预测进行加权综合得最终预测，此集成方式有正则化效果，如当recursive深度过深时浅层输出有较大权重而深层输出权重较小。

### Skip-Connection

Reconstruction网络亦使用原始低分辨率插值图片作为输入，这样仅需要对残差细节部分进行学习，
\begin{equation}
    \hat{y}_d = f_3(x, g^{(d)}(f_1(x))) \\
    \hat{y} = \sum_{d=1}^{D}w_d * \hat{y}_d
\end{equation}

## Loss
网络使用MSE来计算高清图片与预测结果的误差，包含D个输出重构结果与集成预测结果两部分loss。
\begin{equation}
    l_1(\theta) = \sum_{d=1}^{D}\sum_{i=1}^{N}\frac{1}{2DN}||y^{(i)} - \hat{y}_d^{(i)}||^2 \\
    l_2(\theta) = \sum_{i=1}^{N}\frac{1}{2N}||y^{(i)} - \sum_{d=1}^{D}w_d * \hat{y}_d^{(i)}||^2 \\
    L(\theta) = \alpha l_1(\theta) + (1-\alpha) l_2(\theta) + \beta ||\theta||^2
\end{equation}
其中N为样本数，D为Inference中递归次数，文章中使用D=16，网络共20层，感受野为$41 \times 41$。初始时使用较大的$\alpha$以保证训练稳定，逐渐减小$\alpha$值来提升最终性能。

# 参考文献

{% blockquote %}

Kim, J., Kwon Lee, J., & Mu Lee, K. (2016). Deeply-recursive convolutional network for image super-resolution. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1637-1645).

{% endblockquote %}
