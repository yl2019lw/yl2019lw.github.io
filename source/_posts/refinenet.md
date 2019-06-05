---
title: RefineNet:Multi-Path Refinement Networks for High-Resolution Semantic Segmentation
tags:
  - CVPR2017
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-06-05 12:58:52
---

# Introduction

文章提出RefineNet进行图像语义分割，其采用multi-path残差连接充分融合了粗粒度高层语义信息与细粒度浅层信息，获得了state-of-the-art。

常规全卷积网络中使用deconvolution进行语义分割，其已在卷积的下采样过程中丢失了位置细节信息。Dialated convolution需要维护较高分辨率的特征图，内存资源占用较多。

RefineNet采用Identity Mapping版本ResNet作为基础网络结构，递归地利用浅层block的输出结果来refine根据高层block输出进行预测的结果，模型示意图如下：

{% asset_img multi-path.png multi path refinement %}

对应ResNet共4个阶段的输出结果进行4次Refinement，网络最终通过softmax进行逐像素分类，获得的原图1/4大小的预测结果通过双线性插值上采样至原输入大小。

# RefineNet

RefineNet 通过Multi-Path Refinement来逐步融合信息, 迭代过程从ResNet最深层block开始逐渐向上，初始Refine步仅使用ResNet输出作为输入，后续使用高分辨率的ResNet block输出与前次低分辨率的RefineNet block输出作为输入，通过RefineNet block获取本轮次输出。RefineNet block结构如下：

{% asset_img refinenet.png refinenet %}

对于RefineNet block的每个路径的输入，均使用2个并列的RCU单元进行特征调整，其中每个RCU单元形如去除batch norm的基本resnet残差单元。

Adaptive conv的结果依据其特征图的大小进行相应级别的上采样，然后通过sum将来自多个路径的信息进行融合。

融合后的结果通过Chained Residual Pooling进行不同尺度级别的特征聚合，Chained Residual Pooling级联多个残差CRP单元，每个CRP单元为5x5 pooling接3x3卷积。每个CRP单元的输出作为下一CRP单元的输入，所有CRP单元的输出相加作为Chained Residual Pooling的输出。

最终再使用1个RCU单元得到本次RefineNet block的结果。

# 参考文献

{% blockquote %}

Lin, G., Milan, A., Shen, C., & Reid, I. (2017). Refinenet: Multi-path refinement networks for high-resolution semantic segmentation. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1925-1934).

{% endblockquote %}
