---
title: DenseNet:Densely Connected Convolutional Networks
date: 2019-03-23 12:47:52
tags:
  - CVPR2017
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍
文章提出了DenseNet模型，其中Dense指神经网络层与层之间的连接。在常规的卷积神经网络中，L层的网络共有L个连接，在DenseNet中L层的网络有$\frac{L(L+1)}{2}$个连接。在feature map大小匹配的层中，每一层都把其前面所有层的feature map作为其输入，也把其自己的feature
map作为后续所有层的输入，Dense Block如下图所示：

{% img /densenet/denseblock.png 400 dense block %}

DenseNet因其密集地连接方式，能够缓解梯度消失问题，强化特征传播与特征复用，事实上减少了参数数量。DenseNet中每一层都可以相对直接地获取从loss传来的梯度信号及原始输入信息，更好的信息与梯度流动让模型易于训练。DenseNet在CIFAR-10，CIFAR-100，SVHN，ImageNet等数据集上获得了state-of-the-art表现。

# DenseNet

DenseNet由一系列Dense Block组成，每一个Dense Block内具有相同大小的feature map，不同的feature map大小的Dense Block之间通过Transition layer来连接，如下图所示：

{% asset_img densenet.png densenet %}

* Dense Block

    对于ResNet的残差块有：
\begin{equation}
    x_l = H_l(x_{l-1}) + x_{l-1}
\end{equation}
    其中$x_{l-1}$表示第$l-1$层的输入，H是由Convluation, Batch Normalization, Relu组合而成的pre-activation残差函数(堆叠顺序为BN-Relu-Conv)。

    Dense Block沿用Resnet的设计，区别为其接受前面所有层的feature map作为输入：
\begin{equation}
    x_l = H_l([x_0, x_1, ..., x_{l-1}])
\end{equation}

    其中$[x_0, x_1, ..., x_{l-1}]$表示将0到$l-1$层的feature map在通道维concat起来。

* Transition Layer

    Transition layer 连接不同feature map大小的Dense Block，其包含batch normalization, 1x1 conv layer和 2x2 average pooling。

如果$H_l$产生k个feature map，则第l层将有$k_0+k(l-1)$个feature map作为输入，此处k为growth rate。DenseNet与此前的网络不同之处在于可以拥有非常narrow的层获得较好性能，如取k=12。

尽管每层只产生k个feature map，但其拥有非常多的输入feature map。同bottleneck版本resnet一样，可以先使用1x1卷积来降低维度提高计算效率，指定此种版本为DenseNet-B(BN-Relu-Conv(1x1)-BN-Relu-Conv(3x3))。同时也可以在transition layer降低维度，此种版本为DensetNet-C。

在ImageNet数据集上运行的类似Resnet结构的Densent组成如下表：

{% asset_img architecture.png architecture %}

使用DenseNet在ImageNet运行实验结果如下表，可见Denset有更高的参数使用效率，如DenseNet-201参数与Resnet-34相仿性能却与Resnet-101相差无几。
{% asset_img imagenet.png imagenet %}

# 总结
DenseNet允许学到的feature map被后续所有层高效地访问，导致很好的特征复用与紧凑的模型。其中loss回传的梯度信息只需要经过很少的transition layer，有更短的路径，这相当于一种Deep Supervision。在Stochastic
depth网络中，随机drop一些layer后也相当于创建了不同layer的直接连接，这表明DenseNet也是一种有效的正则化方式。总而言之，DenseNet可以通过更少的参数与计算量获得更佳的性能。

# 参考文献
{% blockquote %}

Huang, G., Liu, Z., Van Der Maaten, L., & Weinberger, K. Q. (2017). Densely connected convolutional networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4700-4708).

{% endblockquote %}
