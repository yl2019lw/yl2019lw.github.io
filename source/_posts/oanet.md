---
title: OANet:An End-to-End Network for Panoptic Segmentation
tags:
  - CVPR2019
  - segmentation
category:
  - computer vision
  - segmentation
  - panoptic segmentation
date: 2019-06-17 20:34:56
---

# Introduction

文章提出Occlusion Aware Network来进行全景分割，其可通过统一的网络高效地同时进行语义分割与实例分割。

文章提出spatial ranking module来解决实例与语义预测结果合并中的冲突问题。

# OANet
## Architecture

OANet基于采取FPN作为backbone的Mask R-CNN来进行实例分割，同时共用基础网络特征增加语义分割分支，最终通过设计的spatial ranking模块合并语义分割与实例分割结果。

{% asset_img oanet.png OANet %}

语义分割分支使用FPN所有pyramid level的特征，示意图如下：

{% img /oanet/stuff.png 500 OANet stuff segmentation %}

## Spatial Ranking Module

用于实例分割的Mask R-CNN存在实例间重叠问题，panoptic segmentation启发式地直接根据检测出物体的confidence score进行合并并不完全正确。

如文章所例，由于领带出现的频率远小于人，检测时人的score远高于领带，导致合并结果时丢失了领带。

spatial ranking module为每个object生成其spatial ranking score，合并结果时根据spatial ranking score来进行NMS，可有效解决上述问题。

{% asset_img srm.png spatial ranking module %}

对实例分割每个RoI得到的segmentaion mask，首先将其映射为输入大小的二值化tensor。

该tensor每个分类类别占用一个通道，segment内容映射至其对应类别通道上，实例内部location处的值为1, 否则为0。然后以该tensor为输入，使用large kernel conv得到spatial score map。

spatial score map中该segment对应的类别通道score map为可用，对此可用score map中实例内部部分score求平均即可得此物体的ranking score。

# 参考文献

{% blockquote %}

Liu, H., Peng, C., Yu, C., Wang, J., Liu, X., Yu, G., & Jiang, W. (2019). An End-to-End Network for Panoptic Segmentation. arXiv preprint arXiv:1903.05027.

{% endblockquote %}
