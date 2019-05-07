---
title: SRDenseNet:Image Super-Resolution Using Dense Skip Connections
tags:
  - ICCV2017
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-07 19:14:02
mathjax: true
---
# 介绍

文章提出SRDenseNet完成超分辨率任务，其在较深的网络中使用dense skip connections，高效地利用了低级别与高级别特征信息来提升重构性能，同时有效地缓解了梯度消失问题。文章在网络末端使用deconvolution layer来学习上采样filter，使得网络训练发生于低分辨率空间，能够减少计算量同时获得相对更大的感受野。

# SRDenseNet

SRDenseNet主要包括low level features, high level features, deconvolution layers, reconstruction layer四个部分。其直接使用低分辨率图片作为输入通过卷积层学习较低层次特征表示，然后经过一系列dense block学习较高级别特征表示，再通过两层deconvolution layers学习上采样参数进行四倍放大，最终通过reconstruction layers获取对应高分辨率预测。

文章给出了三个版本的SRDenseNet模型，分别是SRDenseNet_H, SRDenseNet_HL, SRDenseNet_All，其中SRDenseNet_H仅使用最后一层high level特征输入deconvolution layers，SRDenseNet_HL使用了最终层high level特征及low level特征，SRDenseNet_All使用了low level特征及全部DenseNet block输出的high level特征。模型示意图如下：

{% asset_img srdensenet.png srdensenet %}

其中SRDenseNet_All因使用了全部DenseNet block输出的high level特征，feature map较多，此时同resnet bottleneck版本先使用$1 \times 1$卷积来降低通道数。

在提取high level特征部分使用DenseNet block，其每一个节点将同一块内之前的全部节点的输出拼接起来作为其输入，每个节点输出固定通道数目的feature map，此数目为growth rate。DenseNet block示意图如下：

{% asset_img densenet.png densenet %}

文章中使用的growth rate为16，每个块内包含8个节点，每个节点均为$3 \times 3$卷积，故每个DenseNet block输出128维的feature map。SRDenseNet中共使用了8个DenseNet block，因此SRDenseNet_All网络共69层。

# 参考文献

{% blockquote %}

Tong, T., Li, G., Liu, X., & Gao, Q. (2017). Image super-resolution using dense skip connections. In Proceedings of the IEEE International Conference on Computer Vision (pp. 4799-4807).

{% endblockquote %}
