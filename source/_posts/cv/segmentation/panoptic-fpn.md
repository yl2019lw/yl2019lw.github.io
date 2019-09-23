---
title: Panoptic Feature Pyramid Networks
tags:
  - CVPR2019
  - segmentation
category:
  - computer vision
  - segmentation
  - panoptic segmentation
date: 2019-06-17 13:08:56
---

# Introduction

文章提出Panoptic FPN，其使用统一的网络来同时进行语义分割与实例分割，共享特征部分计算并获得较好的性能。

其通过采用了FPN作为骨干网络的Mask R-CNN来进行实例分割，在此基础上增加轻量级地语义分割分支。

<div class='img-size-half'>
{% asset_img panoptic-fpn.png panoptic fpn %}
</div>

Panoptic FPN使用Panoptic Quality作为全景分割评价指标，使用panoptic segmentation中启发式方法融合语义分割与实例分割结果。

# Panoptic FPN

## FPN
FPN对来自ResNet不同stage输出的不同分辨率大小的feature map作为输入，增加带有lateral connection的top down pathway。

其自顶向下的路径从网络最深层开始，在逐渐上采样的同时加上从bottom-up pathway得到的高分辨率feature map的转换版本。

FPN生成特征金字塔，通常尺度为原输入的1/32至1/4，每个pyramid level的feature map有相同的通道数。

## Instance segmentation branch

FPN的不同pyramid level有相同的通道数，使得如Faster R-CNN类基于region的object detector容易关联至某一level特征。

Faster R-CNN根据目标大小在对应pyramid level执行RoI pooling，然后通过共享的分支对每个region进行refine box及预测分类。

为进行实例分割采用Mask-RCNN，其在Faster R-CNN基础上增加全卷积分支为每个候选region预测binary segmentation mask。

## Panoptic FPN

为获取较高的准确率，所采用的特征需要有较高的分辨率来捕获细节信息，有充足的语义信息来准确预测类别，能捕获多尺度信息来预测不同分辨率的stuff region。

Panoptic FPN采用基于FPN的Mask R-CNN，并将其扩展以进行pixel-wise语义分割。

## Semantic segmentation branch

为了预测语义标记，文章合并了从FPN的所有pyramid level获取到的特征信息。

<div class='img-size-half'>
{% asset_img semantic.png panoptic fpn semantic branch %}
</div>

从FPN的最深层特征开始(1/32 scale)，经过三个上采样阶段最终至1/4 scale。每个上采样阶段包含3x3卷积，group norm, ReLU及2倍双线性上采样。

其余FPN pyramid level也进行相应数目的上采样阶段至1/4 scale，然后所有level的输出进行element-wise sum来融合。

最终对sum结果使用1x1卷积, 4倍双线性上采样，softmax来生成原始分辨率大小的逐像素语义分类。

# 参考文献

{% blockquote %}

Kirillov, A., Girshick, R., He, K., & Dollár, P. (2019). Panoptic Feature Pyramid Networks. arXiv preprint arXiv:1901.02446.

{% endblockquote %}
