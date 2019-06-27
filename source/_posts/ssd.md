---
title: SSD:Single Shot MultiBox Detector
tags:
  - ECCV2016 
  - detection
category:
  - computer vision
  - detection
  - one-stage detector
date: 2019-06-27 08:36:47
---

# 介绍

文章提出SSD使用单一深度神经网络在feature map的每个location根据不同尺寸、长宽比的default box来预测类别score与位置偏差。

SSD根据不同分辨率大小的feature map上的default box来进行预测，可自然地处理不同尺度大小的物体。

SSD不需要先生成proposal再进行refinement，直接将所有运算封装在单一网络内，其速度比YOLO更快且性能接近Faster R-CNN。

# Single Shot Detector

与Faster R-CNN类似，SSD在feature map的每个位置密集地生成一系列不同尺度与长宽比的anchor box进行预测。

但不同与Faster R-CNN，SSD无region proposal过程，直接对每个anchor box预测物体分类与位置偏移作为最终结果。

同时SSD使用了不同分辨率大小的feature map，较小的物体由浅层feature map负责，较大的物体由深层feature map负责。

{% asset_img ssd.png SSD %}

SSD基于default box进行adjustment，其与YOLO对比如下所示。

{% asset_img ssd_vs_yolo.png SSD vs YOLO %}

SSD训练时其损失函数包括分类损失与定位损失，分类损失为多类别softmax交叉熵，定位损失同Faster R-CNN中Smooth L1 Loss。

{% asset_img loss.png SSD Loss %}

# 参考文献

{% blockquote %}

Liu, W., Anguelov, D., Erhan, D., Szegedy, C., Reed, S., Fu, C. Y., & Berg, A. C. (2016, October). Ssd: Single shot multibox detector. In European conference on computer vision (pp. 21-37). Springer, Cham.

{% endblockquote %}
