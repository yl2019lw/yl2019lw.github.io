---
title: Large Kernel Matters-Improve Semantic Segmentation by Global Convolution Network
tags:
  - CVPR2017
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-06-06 16:36:12
---

# Introduction

文章提出了Global Convolutional Network来解决语义分割问题，其认为在逐像素预测中存在同时分类与定位，此两问题对于location translation的需求是对立的，当前的语义分割全卷积网络多关注location问题，辅助使用较大的卷积核对应较大的感受野，可提升最终性能。

文章同时提出了基于残差连接方式的boundary refinement来替代dense CRF类后处理过程。

# Global Convolution Network

模型整体示意图及提出的GCN及Boundary refinement如下：

{% asset_img gcn.png gcn %}

其使用ResNet作为骨干网络，层次化地使用4个阶段的输出特征。从ResNet最深层开始迭代，将通过GCN与Boundary Refinement的处理结果与上层阶段处理结果相加，相加前通过上采样使特征图大小匹配。

GCN为所谓的大卷积核，其对应kxk大小的感觉野，采取Inception网络中非对称卷积方式实现，即通过两支并列的kx1及1xk卷积来替代，可有效减少参数数量。

GCN输出通道为目标类别数，可视为语义分割打分，再输入BR模块进行后处理。BR模块为如图示两层堆叠的残差单元。

# 参考文献

{% blockquote %}

Peng, C., Zhang, X., Yu, G., Luo, G., & Sun, J. (2017). Large Kernel Matters--Improve Semantic Segmentation by Global Convolutional Network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4353-4361).

{% endblockquote %}
