---
title: U-Net:Convolutional Networks for Biomedical Image Segmentation
tags:
  - MICCAI2015
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-04-26 15:15:07
---

# 介绍

文章提出了U-Net网络结构用于医学图像语义分割，获得了ISBI竞赛冠军。

# U-net
U-Net网络由contracting path以及expand path,形如字母U，网络结构如图所示：

{% img /unet/unet.png 600 unet %}

其中contracting path为卷积与池化层，feature map大小变小，通道数翻倍，卷积过程中边缘未padding被crop掉。expand path通过转置卷积进行上采样，过程中会将contracting path中对应feature map裁剪到同样的大小进行concat，综合利用了不同粒度的信息，最终通过1x1卷积映射到类别scroe map。

因医学图像较大，采用了重叠的patch进行seamless的预测，即使用比要预测的部位大的patch作为输入，只取中间部位的输出。

{% img /unet/seamless.png 600 seamless %}

对于紧挨在一起的细胞，使用加权loss处理边缘信息，文章对每个位置结合类别频率计算了相应的权重。

# 参考文献

{% blockquote %}

Ronneberger, O., Fischer, P., & Brox, T. (2015, October). U-net: Convolutional networks for biomedical image segmentation. In International Conference on Medical image computing and computer-assisted intervention (pp. 234-241). Springer, Cham.

{% endblockquote %}
