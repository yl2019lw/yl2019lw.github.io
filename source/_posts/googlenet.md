---
title: GoogLeNet:Going Deeper with Convolutions
date: 2019-03-19 16:25:44
tags:
  - CVPR2015
  - classification
category:
  - computer vision
  - classification
---


# 介绍

GoogLeNet模型由Google团队提出，在ILSVRC-2014比赛中获得分类任务第一名。GoogLeNet设计了Inception模块，也即Inception-V1版本。

# Inception

Inception模块使用不同大小的卷积核提取特征并组合在一起实现不同尺度的特征融合。
其中1x1,3x3,5x5卷积核使用的padding分别为0,1,2,保证了卷积前后feature map大小完全一致，3x3 max pooling步长为1，可以直接在通道维度拼接不同卷积的结果。

为减少计算量，在3x3及5x5卷积核前引入1x1卷积调整通道数目降低维度, 3x3 max pooling后也引入1x1卷积降维， 如下图所示。

<div class='img-size-half'>
{% asset_img inception.png inception%}
</div>

# GoogleNet

GoogLeNet将相同结构的Inception模块堆叠起来，共22层，模型结构如下：

其中输入为224x224x3, 首先仍然使用传统7x7卷积，然后依据feature map大小引入三个层次的Inception模块，分别为inception(3a,3b,4a,4b,4c,4d,4e,5a,5b)，前面的数字表示相对于原始输入特征大小缩小了2的几次方倍，字母a,b等表示级联的顺序，前一个Inception模块融合后的结果将作为下一个Inception模块的输入。不同层次的Inception模块间通过步长为2的max
pooling特征图大小减半。最终通过softmax实现多分类任务。

{% asset_img googlenet-arch.png This is googlenet architecture%}

因网络较深，不利于loss反向传递更新参数，在Inception(4a, 4d) 后引入了两个小的网络进行辅助训练。辅助网络同样进行多分类，不参与模型测试。
模型整体流程见下图：

{% asset_img googlenet.jpg This is googlenet architecture%}

# 结果

在ILSVRC-2014分类任务上获得冠军，top-5 error 为6.67%。

# 参考文献

{% blockquote %}

Szegedy, C., Liu, W., Jia, Y., Sermanet, P., Reed, S., Anguelov, D., ... & Rabinovich, A. (2015). Going deeper with convolutions. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1-9).

{% endblockquote %}
