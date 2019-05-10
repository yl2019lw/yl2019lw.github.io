---
title: Mask R-CNN
tags:
  - ICCV2017
  - segmentation
category:
  - computer vision
  - segmentation
  - instance segmentation
date: 2019-04-18 21:03:21
mathjax: true
---

# 介绍

Mask R-CNN在Faster R-CNN基础上增加了mask分支，能够高效地对每个RoI预测其mask，赢得coco数据集上目标检测，实例分割及人体关键点检测任务冠军。Mask R-CNN整体网络结构如下所示：

{% img /mask-rcnn/mask-rcnn.png 600 mask r-cnn %}

# Mask R-CNN

Faster R-CNN对每个候选物体预测两个分支：分类与边框偏差。Mask R-CNN并行增加了预测候选物体mask的分支。在训练时每个RoI的损失为$L = L_{cls} + L_{box} + L_{mask}$，其中$L_{cls},L_{box}$与Faster R-CNN的分类与边框回归分支相同，每个RoI的mask分支输出为$Km^2$维，K为物体类别数，m为预测的掩码图大小，即为每个类都预测$m \times m$的二值掩码，损失函数为二元交叉熵，对K个mask仅使用该Roi对应的ground truth类别的mask参与计算损失。当预测时使用分类分支输出的类别对应mask作为实例mask。

mask编码了物体的详细位置信息，因此不同于分类与box回归使用全连接层，很自然地使用全卷积网络来实现。mask head使用了deconv来使其预测的mask变大，如文章通过RoI Align得到$7 \times 7$大小的RoI特征表示，输出mask大小为$28 \times 28$。

由于mask为逐像素的预测，需要很精准的位置信息，文章提出了Roi Align来为每个Roi提取固定大小的特征。RoI Pooling过程中因两次量化会带来位置上的偏差，第一次发生于骨干特征提取网络从输入图片级到终点feature map级，此时因卷积过程中feature map不断减半带来的潜在量化误差（如round(x/16)）；第二次发生于从feature map上对应位置处进行相对pooling获取固定大小输出时，此时为划分成固定数目的格子进行max pooling也有不能整除的问题。RoI Align在执行对应除法时不进行取整，得到的位置点所对应的值由相邻的四个规范点通过双线性插值确定。

双线性插值计算方式如下：

{% img /mask-rcnn/BilinearInterpolation.png 300 bilinear point %}

{% img /mask-rcnn/bilinear.png 600 bilinear calculate %}

# 参考文献

{% blockquote %}

He, K., Gkioxari, G., Dollár, P., & Girshick, R. (2017). Mask r-cnn. In Proceedings of the IEEE international conference on computer vision (pp. 2961-2969).
<br/>
https://en.wikipedia.org/wiki/Bilinear_interpolation

{% endblockquote %}
