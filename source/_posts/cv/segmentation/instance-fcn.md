---
title: InstanceFCN:Instance-sensitive Fully Convolutional Networks
tags:
  - ECCV2016 
  - segmentation
category:
  - computer vision
  - segmentation
  - instance segmentation
date: 2019-06-12 14:30:45
mathjax: true
---

# Introduction

文章提出InstanceFCN采用全卷积网络来生成instance-sensitive segment proposal。

不同于语义分割使用全卷积网络仅生成一个score map，Instance FCN为在物体当中的可能的每个相对位置都生成像素级score map。

文章中采用3x3格子共9个相对位置生成9个score map, 然后再使用聚集模块合并一系列score map的结果，根据输入图片的每个位置生成潜在的instance candidate。

{% asset_img instance-fcn.png instance fcn %}

# InstanceFCN

## Instance-sensitive score map 

当输入图像仅包含一个物体时，FCN输出的语义分割结果即可提供该物体类别的segmentation proposal。

当输入图像包含两个物体时，FCN的大部分输出仍然可用, 仅两个物体相交的部分需要重新处理。当可区分相交的一个物体的右部分与另一个物体的左部分时，FCN输出的score map可用于生成实例。

既然如此，一个像素可能位于某个物体的某个相对位置，如左上，中间，右下等，则不同于FCN仅生成一个score map，对每个像素均生成全部相对位置的score map。

对于输入图像包含两个左右相交物体对应的某同一位置，左边物体采用该位置处对应的多个score map中相对左位置的score map，右边物体采用相对右位置的score map。

文章采用$m \times m$大小的滑动窗口来聚合score map，聚合输入为$k \times k$个score map，滑动窗口对应$\frac{m}{k} \times \frac{m}{k}$个子窗口，输出score map内容为从对应子窗口score map拷贝而来。

此score map聚合过程没有需要学习的参数，直接将多个score map的内容根据其相对位置拼贴成最终的score map。

训练时稀疏地选择slide window，测试时密集地使用slide window。

InstanceFCN兼顾了图像中的Local coherence，即当图像内容有微小偏移时其预测内容不变，而DeepMask输出mask时使用扁平化的全连接层，微小偏移带来全部参数的重新学习与不同的输出结果。

# Architecture

InstanceFCN 模型架构如下：

{% asset_img architecture.png InstanceFCN architecture %}

其使用全卷积网络来识别不同的instance，采用Vgg16中前13个卷积层提取特征，末端采用孔洞卷积和更小的卷积步长来维持感受野及输出分辨率。

在feature map之上包括两个卷积分支，其中一分支采用前述过程生成instance-sensitive score map作为segmentation proposal，另一分支在每个位置处输出以该位置为中心的slide window 是否为物体的得分。

损失函数为：
\begin{equation}
    \sum_i(L_(p_i, p_i^*) + \sum_jL(S_{i,j}, S_{i, j}^*))
\end{equation}

其中i为slide window标号，$p_i$为objectness score, j为window内像素编号，$S_{i}$为预测的segmentation proposal。

InstanceFCN输出再结合下游分类器即可实现完整的实例分割。

# 参考文献

{% blockquote %}

Dai, J., He, K., Li, Y., Ren, S., & Sun, J. (2016, October). Instance-sensitive fully convolutional networks. In European Conference on Computer Vision (pp. 534-549). Springer, Cham.

{% endblockquote %}
