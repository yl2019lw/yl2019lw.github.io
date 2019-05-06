---
title: ESPCN:Real-Time Single Image and Video Super-Resolution Using an Efficient Sub-Pixel Convolutional Neural Network
tags:
  - CVPR2016
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-06 16:41:46
mathjax: true
---

# 介绍

文章提出ESPCN解决超分辨率任务，直接使用原始低分辨率图片输入网络进行学习，在网络的最终层使用Sub-Pixel卷积调整为高分辨率图像大小，大幅提升了处理速度。

# ESPCN

不同于SRCNN，ESPCN网络输入为原始低分辨率图片，不需要经过双三次插值放大至对应高清图片大小，在网络的深层进行放大操作。因输入图片变小，大幅减少了运算复杂度及内存需要，对视频进行实时还原成为可能。对一个$L$层的网络，在最终层学习$n_{L-1}$(L-1层feature map数量)上采样filter，较SRCNN使用的1个双三次插值filter更丰富，使得网络可更好地学习到低分辨率到高分辨率的映射关系。

{% asset_img espcn.png espcn %}

对于$L$层网络，前$L-1$层可表示为：
\begin{equation}
    f^1(I^{LR}; W_1, b_1) = \phi(W_1 * I^{LR} + b_1) \\
    f^l(I^{LR}; W_{1:l}, b_{1:l}) = \phi(W_l * f^{l-1}(I^{LR}) + b_l)
\end{equation}

最终层使用Sub-Pixel卷积：
\begin{equation}
    I^{SR} = f^L(I^{LR}) = PS(W_L * f^{L-1}(I^{LR}) + b_L) \\
    PS(T)_{x, y, c} = T_{\lfloor{x/r}\rfloor,  \lfloor{y/r}\rfloor,  c \cdot r \cdot mod(y,r) + c \cdot mod(x,r)}
\end{equation}
其中$W_L$形如$n_{L-1} \times r^2C \times k_L \times k_L$，pixel shuffling将$H \times W \times C \cdot r^2$的张量重排为$rH \times rW \times C$，$r$为缩放因子，即调整了低分辨率到高分辨率图像大小。

损失函数为：
\begin{equation}
    l(W_{1:L}, b_{1:L}) = \frac{1}{r^2HW}\sum_{x=1}^{rH}\sum_{y=1}^{rW}(I_{x,y}^{HR} - f_{x,y}^L(I^{LR}))^2
\end{equation}


# 参考文献

{% blockquote %}

Shi, W., Caballero, J., Huszár, F., Totz, J., Aitken, A. P., Bishop, R., ... & Wang, Z. (2016). Real-time single image and video super-resolution using an efficient sub-pixel convolutional neural network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1874-1883).

{% endblockquote %}
