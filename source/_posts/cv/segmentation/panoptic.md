---
title: Panoptic Segmentation
tags:
  - CVPR2019
  - segmentation
category:
  - computer vision
  - segmentation
  - panoptic segmentation
date: 2019-06-17 12:08:56
mathjax: true
---

# Introduction

文章提出了全景分割任务，其既要进行语义分割也要进行实例分割。对图像中的每一个像素，输出其语义类别与实例id。语义label与实例id相同的像素为同一物体，对于stuff类别实例id应忽略。

<div class='img-size-half'>
{% asset_img panoptic.png panoptic %}
</div>

不同于语义分割，全景分割需要区分出不同的实例，这对于全卷积网络是个挑战。不同于实例分割，全景分割要求实例之间不能重叠，这对于基于RoI的实例分割方法也是挑战。

文章亦针对全景分割任务提出了度量指标panoptic quality。

当前全景分割任务使用数据集有Cityscapes，ADE20K，Mapillary Vistas以及COCO。

# Panoptic segmentation

## metric

前景物体为things类别，需进行实例分割，背景物体为stuff类别，需进行语义分割。things与stuff无交叉，全景分割为每一个像素输出语义label及实例id。

在segment匹配过程中，仅当predicted segment与ground truth的IoU大于0.5时认定匹配成功，且这种匹配是唯一的。

如果有两个predict都与gt的IoU超过0.5，那么这两个predicted segment必然有重叠，与每一个像素有明确唯一的语义类别和实例id相矛盾。

由于匹配时ground truth segment与predicted segment的对称性，也仅有一个ground truth segment可与predicted segment有大于0.5的IoU。

<div class='img-size-half'>
{% asset_img match.png match %}
</div>

对thing与stuff中每一个类别，segment匹配结果可分为TP,FP,FN，可计算如下PQ指标：

\begin{equation}
    PQ = \frac{\sum_{(p, q) \in TP}IoU(p, g)}{|TP| + \frac{1}{2}|FP| + \frac{1}{2}|FN|}
\end{equation}

亦有如下形式：
\begin{equation}
    PQ = \frac{\sum_{(p, q) \in TP}IoU(p, g)}{|TP|} \times \frac{|TP|}{|TP| + \frac{1}{2}|FP| + \frac{1}{2}|FN|}
\end{equation}

其中第一项为segmentation quality, SQ表征了对于成功匹配的segment其平均意义下的IoU。
第二项为recognition quality, RQ即为检测中常用的F1值。SQ依赖于RQ。

PQ计算过程中忽略void labels，void labels可源于out of class pixels或ambiguous or unknown pixels。

PQ treats all classes(stuff and things) in a uniform way。

## segmentation

文章通过融合PSPNet语义分割结果与Mask R-CNN实例分割结果来生成最终的全景分割结果。

对于实例分割算法产生的重叠segments，根据其confidence score排序结果进行NMS过程。

对每个实例首先移除已被更高score的实例占有的pixel，如果剩下的segment部分还有足够大的IoU则保留这部分非重叠的赋值。

语义分割结果自身无重叠，可直接进行使用，其与实例分割重叠时以实例分割结果为准。

# 参考文献

{% blockquote %}

Kirillov, A., He, K., Girshick, R., Rother, C., & Dollár, P. (2018). Panoptic segmentation. arXiv preprint arXiv:1801.00868.

{% endblockquote %}
