---
title: FPN:Feature Pyramid Networks for Object Detection
tags:
  - CVPR2017
  - detection
category:
  - computer vision
  - detection
date: 2019-04-16 21:09:15
mathjax: true
---

# 介绍
文章提出了Feature Pyramid Network，利用卷积神经网络内在的特征层次性，以低成本的方式实现了利用多个尺度的特征，在多个应用上显示了作为特征提取方式的优越性。
FPN有效地融合了高层的low-resolution,semantically strong特征与浅层的high-resolution,semantically weak特征，FPN与其它利用多尺度特征方式对比如下图：

{% img /2019/04/16/fpn/fpn.png 500 fpn %}

# Feature Pyramid Networks
FPN将单一尺度的图片作为输入，输出多个尺度的特征表示。其通过全卷积方式提取特征，当输入大小变化时，对应的feature map也成比例的变化大小。通常将resnet特征提取器称作backbone，检测与分割网络分支为head，则FPN为网络结构中的neck。FPN包含bottom-up pathway, top-down pathway及lateral conections 融合浅层的细粒度特征与深层强语义特征，示意图如下：

{% img /2019/04/16/fpn/fusion.png 500 fusion %}

## Bottom-up pathway
自底向上通路为常规的特征提取网络结构，如对resnet而言，根据不同网络深度中feature map的大小可以划分为4个stage，后一stage的特征图大小的前一stage的一半。文章取每一stage的最后一个残差单元的feature map作为该stage的代表，组合成有层次结构的特征金字塔，对应conv2, conv3, conv4, conv5的输出标记为C2,C3,C4,C5。

## Top-down pathway and lateral connections
自顶向下通路及横向连接进行迭代式的特征融合。自顶向下指将深层较小的特征图进行上采样2倍后作为输入构造对应浅层特征图，横向连接即将大小相同的top-down pathway与bottom-up pathway中的特征图进行相加。所有top-down pathway特征图有相同的通道数，如256-d。从横向连接拿过来的bottom-up pathway的特征图需要首先通过1x1卷积使通道数目匹配。迭代式地执行此过程可构造与C2,C3,C4,C5大小相等的对应特征图P2,P3,P4,P5,初始的P5由C5进行1x1卷积后获得，相加后的特征通过3x3卷积来消除aliasing effect。

# Application

## Feature Pyramid Networks for RPN

## Feature Pyramid Networks for Fast R-CNN

# Experiments

## Dataset

## Ablation Experiments

{% asset_img rpn-res.png rpn result %}

{% asset_img fastrcnn-res.png fast r-cnn result %}


# 参考文献
{% blockquote %}

Lin, T. Y., Dollár, P., Girshick, R., He, K., Hariharan, B., & Belongie, S. (2017). Feature pyramid networks for object detection. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2117-2125).

{% endblockquote %}
