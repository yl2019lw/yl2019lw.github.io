---
title: SPP-net:Spatial Pyramid Pooling in Deep Convolutional Networks for Visual Recognition
tags:
  - TPAMI2015
  - detection
category:
  - computer vision
  - detection
date: 2019-04-09 19:03:22
mathjax: true
---

# 介绍

通常卷积神经网络需要固定大小的输入，文章提出了spatial pyramid pooling可以处理任意大小的输入并产生固定大小的输出。对于检测任务，SPP-net仅在图像级别计算一次feature map，然后在不同大小的子区域进行spatial pyramid pooling得到固定大小的特征表示。相较于R-CNN有更优或相近的精度，但有几十至百倍的速度提升。

类R-CNN系统需要固定大小的输入，这需要对输入图片进行crop或warp，导致出现不能包含完整目标区域或几何形变的情况，影响模型表现。实际上固定大小的输入要求来自于全连接层，因卷积层以滑动方式遍历所有位置，当输入大小变化时会带来对应变化比例的特征图，此时可通过动态窗口大小的池化层来获得固定大小的输出。

文章提出的Spatial pyramid pooling用来替换卷积特征提取部分的最后一个池化层，不需要固定大小的输入，也就没必要对输入图像进行crop或warp。因输入图像大小的变化带来输入SPP特征图大小的变化，多尺度的输入有助于提升泛化能力。同时SPP会使用多个窗口大小的池化，而原始系统中池化窗口大小是固定的，多尺度pooling对object deformation是鲁棒的。

因为SPP是在整张图片上计算一次feature map，而R-CNN对每个region proposal分别计算feature map，因此会大幅提升系统处理速度。

# Spatial Pyramid Pooling

文章使用的SPP应用于最终feature map上替代原pooling层，后面跟上需要固定大小的全连接层作为分类器。SPP接受任意大小的输入并产生固定大小的输出，示意图如下：

{% img /sppnet/sppnet.png 600 sppnet %}

通常的pooling使用固定的窗口大小，当输入大小变化时pooling的输出也对应成比例的变化。换个角度，为得到固定大小的输出，可使用与输入大小成对应比例的pooling窗口大小，因pooling是没有参数的，所以这种操作没有额外代价。
文章使用了多个输出大小级别的pooling，因此被称作spatial pyramid pooling。文章使用ZFNet作为基础网络，最终特征图有256个channel，对应pooling时每一个格子单元的输出即对应256维的向量。文章使用了1x1,2x2,3x3,6x6共四个级别的pooling输出窗口大小，然后把不同pooling的结果拼接起来, 因此最终的特征维度为12800（$12800 = 256 \times 50$, $50 = 1 \times 1 + 2 \times 2 + 3 \times 3 + 6 \times 6$）。

对于通过selective search或edge boxes产生的region proposal，在image level的特征图上映射至对应的子区域，通过SPP得到固定大小的特征表示后提供给全连接层，最终输入每个类特定的SVM进行分类。

# 参考文献

{% blockquote %}

He, K., Zhang, X., Ren, S., & Sun, J. (2015). Spatial pyramid pooling in deep convolutional networks for visual recognition. IEEE transactions on pattern analysis and machine intelligence, 37(9), 1904-1916.

{% endblockquote %}
