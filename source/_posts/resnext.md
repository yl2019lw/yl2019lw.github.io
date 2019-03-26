---
title: ResNeXt:Aggregated Residual Transformations for Deep Neural Networks
date: 2019-03-26 21:36:31
tags:
  - CVPR2017
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍
文章提出了ResNext模型，其结合了Resnet和Inception结构的思想，通过聚合一系列结构相同的块，达到了以更少的参数获得更佳的性能。其中引入了一个新的维度来描述模型复杂程度，Cardinality,指每个处理单元中拓扑结构相同的块的个数，ResNext中Next指的就是这个next dimension。相较于增加模型的深度和宽度，增加模型的cardinality是一种更有效地增加模型表达能力的方式。ResNext模型最终获得ILSVRC-2016上分类任务第二名。

# ResNext

ResNext基本块结构如下：

{% img /2019/03/26/resnext/block.png 600 resnext block %}

左边为ResNet，右边为ResNext，参考了Inception多分支结构，但不同分支拓扑结构相同。二者具有大致相同的参数量和计算复杂度。

ResNext块有如下等价形式，实现中采用第三种方式：

{% asset_img equivalent.png equivalent %}

其中每一层标记为输入通道，卷积大小，输出通道。在相应通道维度进行拼接可见以上三种形式是等价的。

近似复杂度的cardinality与width关系如下：

{% img /2019/03/26/resnext/cardinality_width_relation.png 600 cardinality width relation %}

堆叠而成的ResNext-50 结构如下表：

{% img /2019/03/26/resnext/resnext50.png 600 resnext-50 %}

其中，ResNext-50与ResNet-50参数量和计算复杂度相近。

# Experiments

* Cardinality vs Width

    增加基本块内的cardinality比增加块内width更有效，如下表所示(保持复杂度)：
{% img /2019/03/26/resnext/cardinality_width_result.png 600 cardinality width result %}

* Increasing Cardinality vs Deeper/Wider

    增加cardinality也比加深或加宽网络有更有效的表示能力，32x4d版ResNext-101甚至比其多一倍复杂度的ResNext-200结果还要好。
{% img /2019/03/26/resnext/deeper_wider.png 600  increasing cardinality vs deeper wider %}

* Residual connections

    残差连接也很重要：
{% img /2019/03/26/resnext/residual.png 600 residual connections %}

* performance

    与几个优秀模型在ImageNet数据集上的比较：
{% img /2019/03/26/resnext/imagenet_1k.png 600 imagenet 1000 result %}

# 参考文献
{% blockquote %}

Xie, S., Girshick, R., Dollár, P., Tu, Z., & He, K. (2017). Aggregated residual transformations for deep neural networks. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 1492-1500).

{% endblockquote %}
