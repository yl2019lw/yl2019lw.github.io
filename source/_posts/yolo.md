---
title: You Only Look Once:Unified,Real-Time Object Detection
tags:
  - CVPR2016
  - detection
category:
  - computer vision
  - detection
  - one-stage detector
date: 2019-06-26 20:51:02
mathjax: true
---

# Introduction

文章提出了one-stage实时目标检测器YOLO，其将目标检测任务定义为回归问题，在单趟运行中直接从原始图片预测目标框位置与物体类别。其运行速度快至45 frames每秒，并且性能优于R-CNN及DPM。

YOLO直接基于整张图片作预测，因此其隐式地包含了需要的类别上下文信息与外观信息，导致更少的background error。

YOLO的准确率仍低于两阶段检测器，并且在定位非常小的物体时存在困难。

# YOLO

## Pipeline

YOLO将输入图片划分为大小为$S \times S$的格子，如果某个物体的中心落在一个格子内，则该格子负责预测该物体的位置与类别。

每个格子预测B个bounding box并为每个box预测confidence score。confidence score反映了模型认为该box是否包含物体及预测位置的精确程度。

如果格子内没有物体，则confidence score应该为0，否则其应该反映预测的box与ground truth之间的IoU。

每个bounding box预测内容包含5项：x,y,w,h及confidence。坐标(x,y)表示预测box的中心相对于该格子的偏移，w,h为box相对于整张图片的宽和高。

每个格子同时预测C个条件概率$Pr(Class_i|Object)$，即在该格子包含物体的情况下其从属类别概率。每个格子预测一次类别score，与其预测box数量无关。

模型流程如下，YOLO预测固定大小的输出进行回归。

{% img /yolo/yolo.png 600 YOLO %}

## Architecture

YOLO包含24层卷积与2个全连接层，其间使用了简化版Inception模块结构，即简单堆叠$1 \times 1$卷积与$3 \times 3$卷积。

文章亦提出了Fast YOLO，其使用更少的全连接层。YOLO使用了Leakly ReLU，其架构如下图所示。

{% asset_img architecture.png architecture %}

## Loss

对于回归问题可使用均方误差作为损失函数，但直接使用会同等weight定位误差与分类误差。

同时训练时许多格子内不包含物体，其confidence score优化目标为0，这对于包含物体的格子不利。文章分别为定位误差与不包含物体时confidence损失设置了不同的权重。

均方误差同时同等weigth不同size的预测box，为缓解box大小的影响，文章对box的宽和高预测其平方根。

YOLO为每个格子预测多个box，在训练时每个物体只有一个box负责预测该对象，我们根据与ground truth的IoU值挑选box。

{% img /yolo/loss.png 500 YOLO Loss %}

如上所示为实际使用的损失函数，$1_i^{obj}$标示物体落在格子i，$1_{i,j}^{obj}$标示格子i内第j个box负责预测该物体。

仅在格子内包含物体中心时惩罚分类误差，仅在相应box负责预测物体时惩罚定位误差。



# 参考文献

{% blockquote %}

Redmon, J., Divvala, S., Girshick, R., & Farhadi, A. (2016). You only look once: Unified, real-time object detection. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 779-788).

{% endblockquote %}
