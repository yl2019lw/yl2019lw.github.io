---
title: TASCNet:Learning to Fuse Things and Stuff
tags:
  - arxiv
  - segmentation
category:
  - computer vision
  - segmentation
  - panoptic segmentation
date: 2019-06-20 15:58:56
---

# Introduction

文章提出TASCNet进行端到端地学习来实现全景分割，其采用ResNet50及FPN作为共享骨干网络，后跟两个分支分别进行实例分割和语义分割。

其新提出Things and stuff consistency来约束语义分割与实例分割的结果，使得二者在输出的每个像素位置是否为前景/背景有更好地一致性。

语义分割与实例分割结果仍需要进行类panoptic segmentation方式进行启发式地融合。

# TASCNet

语义分割分支以每个pyramid level的特征作为输入，通过一系列卷积转换后上采样至最大分辨率级别后拼接在一起，然后进行逐像素分类。

实例分割分支采取类Mask R-CNN结构，在基础FPN上额外增加两个pyramid level，TASCNet网络架构如下所示。

{% asset_img tascnet.png TASCNet %}

Things and stuff consistency在于语义分割分支与实例分割分支对每个像素位置所属为前景还是背景的判定是相同的，二者均给出confidence mask。

语义分割分支confidence mask根据其输出score map生成，首先使用0.5进行阈值过滤，余下的像素中预测为things类别的保留，预测为stuff的置0。

实例分割分支为每个RoI生成对应类别的segmentation mask score，可采用下述RoI Flatten操作根据全部RoI的分割结果进行拼贴得到实例分支confidence mask。

<div class='img-size-half'>
{% asset_img roi-flatten.png RoI Flatten %}
</div>

首先构造同输入图像大小的空白tensor，然后将RoI实例对应类别的分割结果使用0.5进行阈值过滤，通过插值到相应大小贴到该tensor相应位置处。

对于多个RoI前景有重叠的像素处取该位置处相应的多个实例的segmentation score均值。

在进行训练时增加L2 loss对语义分支confidence mask与实例分支confidence mask进行约束。

# 参考文献

{% blockquote %}

Li, J., Raventos, A., Bhargava, A., Tagawa, T., & Gaidon, A. (2018). Learning to fuse things and stuff. arXiv preprint arXiv:1812.01192.

{% endblockquote %}
