---
title: Resnet:Deep Residual Learning for Image Recognition
tags:
  - CVPR2016
  - classification
category:
  - computer vision
  - classification
date: 2019-03-19 20:55:40
mathjax: true
---

# 介绍

文章提出残差网络使得能够更容易地训练出更深层次的神经网络，Resnet模型给出了高至152层的网络，但却比VGG有更小的复杂度。Resnet模型横扫了ILSVRC-2015，COCO-2015上的分类、检测、分割等比赛任务的冠军。

更深的神经网络可能带来更好的性能，但却存在难以训练的退化问题，这并不是因为更深的网络导致了过拟合，而是训练误差都不能得到很好地下降，如下图所示：

<div class='img-size-half'>
{% asset_img degradation.png degradation %}
</div>

退化问题说明了深度模型训练的困难。从理论分析来看，考虑一个浅层网络和在其之上加了更多层的深层网络，对于深层网络可以把额外增加的层训练为恒等映射，其余部分从浅层网络拷贝过来，那么对于深层网络就应该存在方案使得其训练误差可以比相应的浅层网络要小。实际训练网络的情况并非如此，本文使用残差学习解决这个问题。

# Deep Residual Learning

## Residual Learning

假设$H(x)$是要通过堆叠的网络层学习的从输入到输出的映射，$x$表示输入，如果假定多层网络能够渐近地学习到$H(x)$，那么其也应该能学习到$F(x)=H(x)-x$。通过残差方式只需要学习$F(x)$，原始问题为$F(x)+x$，两种方式的学习难易程度却有很大的不同。

<div class='img-size-half'>
{% asset_img residual.png degradation %}
</div>

## Identity Mapping by Shortcuts

残差学习可形式化地表达为$y=F(x,{W_i})+x$，其中$x,y$是当前整个块的输入和输出，$F(x,{W_i})$表示需要学习的残差映射。如上图所示为一个残差building block，包含两层，则$F=W_2\sigma(W_1x)$，其中$\sigma$表示Relu函数。由公式可以看出，残差方式没有带来额外的参数，也基本没有带来更多地运算，因此可以公平地与相应地plain network进行比较。当$x$与$F$有相同的维度时，直接进行element-wise相加即可，当维度不同时可对$x$投影到匹配的维度后再进行相加，此时有$y=F(x,{W_i})+W_sx$。当维度本就相同时也可使用方阵形式的$W_s$。

## Network Architecture

* plain network

    plain network如同VGG一样堆叠3x3卷积网络，遵循两个设计原则：

    + 对于输出的feature map大小一样的层有相同数量的卷积核。
    + 如果输出的feature map大小减半，则卷积核的数量翻倍以保持每层的复杂度。

* residual network

    residual network在前述的plain network上插入shortcut connection。当输入与输出维度相同时，可直接使用identity shortcut。当维度不同时，可仍选择shortcut mapping但进行额外补0以使维度匹配，或者使用projection shortcut。当shortcut connection跨越不同大小的feature map时，projection使用步长为2。

* bottleneck

    当配置的网络较深时存在参数过多的问题，因此可引入1x1的卷积核来降低维度减少计算量。具体来说，2层的3x3残差块中可由1x1,3x3,1x1堆叠的三层块代替，前面的1x1卷积用来降低维度，后面的1x1用来提升维度以使输入输出维度匹配，如下图所示：

<div class='img-size-half'>
{% asset_img bottleneck.png bottleneck %}
</div>

* architecture

    常用resnet有18，34，50，101及152层，其中50层及以上使用bottleneck版本的块。resnet首先使用步长为2的7x7卷积，然后包含四组不同feature map大小的堆叠的残差块，因此最终feature map的大小为输入的1/32。resnet中无全连接层，在最终的feature map上执行全局池化后输入softmax进行分类。不同深度的resnet配置如下表：

{% asset_img resnet.png resnet %}

# 参考文献

{% blockquote %}

He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep residual learning for image recognition. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 770-778).

{% endblockquote %}
