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
自顶向下通路及横向连接进行迭代式的特征融合。自顶向下指将深层较小的特征图进行上采样2倍后作为输入构造对应浅层特征图，横向连接即将大小相同的top-down pathway与bottom-up pathway中的特征图进行相加。

所有top-down pathway特征图有相同的通道数，如256-d。从横向连接拿过来的bottom-up pathway的特征图需要首先通过1x1卷积使通道数目匹配。迭代式地执行此过程可构造与C2,C3,C4,C5大小相等的对应特征图P2,P3,P4,P5,初始的P5由C5进行1x1卷积后获得，相加后的特征作为top-down pathway的输入，也通过3x3卷积来消除aliasing effect作为特征金字塔的其中一层输出。

# Application

## Feature Pyramid Networks for RPN
通常RPN网络仅使用最深层feature map作为其参考输入来生成不同尺度与长宽比的proposal，具体而言在一张feature map上密集滑动，使用nxn卷积在每个位置处提取特征向量（同channel维度），然后通过两个分支的1x1卷积为每个anchor生成objectness score和bounding box target。

FPN提供了多个尺度可用的feature map，此时RPN可对应在这多个feature map上密集地生成proposal，过程与单feature map类似。因浅层feature map对应感受野较小可对应小尺寸物体，高层feature map可对应大尺寸物体，因此在每个密集滑动的feature map上可仅生成与其大小对应的anchor，此时仍为每个feature map的每个position生成多个长宽比的proposal。

文章使用了5个level的特征金字塔P2,P2,P3,P4,P5,P6,对应anchor面积为$32^2,64^2,128^2,256^2,512^2$，长宽比为$1:2,1:1,2:1$，即为一个位置FPN生成15个anchor。当anchor与groundtruth box的IoU大于0.7时为正样本，小于0.3时为负样本，groundtruth box与anchor关联，不受使用几层feature map影响，anchor根据其大小被分配至由相应level的feature map生成。

## Feature Pyramid Networks for Fast R-CNN
通常Fast R-CNN也仅使用一张feature map进行Roi Pooling来提取候选区的特征，当与FPN结合时使用多个level的feature map。简单来说，objectness score较高的anchor被认为是可能的proposal,此时根据此proposal的面积大小从对应的单一feature map上进行RoI pooling即可(小的用浅层，大的用深层)，后续bbox head或mask head不变，不同level的参数是共享的。

# Experiments

## Dataset
文章使用了coco数据集，对目标检测问题使用AR及AP指标。不同于Voc使用IoU为0.5来决定true positive，coco使用的AP采取了0.5:0.05:0.95共10个不同的标准来判断box为正样本，这样可获得更高质量的预测。coco也根据物体的大小给出了small,medium,large三个尺度的评价指标，对于AR也评价对单张图片分别预测1，10，100个box时其average recall表现。

## Ablation Experiments
FPN的各组件用于生成region proposal的ablation结果如下：
{% asset_img rpn-res.png rpn result %}

FPN的各组件用于fast r-cnn的ablation结果如下：
{% asset_img fastrcnn-res.png fast r-cnn result %}

# 参考文献
{% blockquote %}

Lin, T. Y., Dollár, P., Girshick, R., He, K., Hariharan, B., & Belongie, S. (2017). Feature pyramid networks for object detection. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2117-2125).

{% endblockquote %}
