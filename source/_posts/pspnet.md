---
title: PSPNet:Pyramid Scene Parsing Network
tags:
  - CVPR2017
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-06-05 22:35:59
---

# Introduction

文章提出PSPNet进行图像语义分割，其可提供global-scene-level prior信息，所谓先验信息如通常汽车不会出现在河面上等。PSPNet使用不同子区域及不同尺度的信息形成层次化全局先验信息，
 模型结构如下：

{% asset_img pspnet.png pspnet %}

网络首先使用卷积网络提取特征图作为Pyrammid Pooling Module的输入，特征提取网络中包含dilated convolution来控制特征图的分辨率及感受野。

Pyramid Pooling Module使用不同大小及步长的pooling window进行池化获取固定大小的bin，再通过1层卷积调整池化后特征维度。将这些调整维度后不同大小的bin进行上采样至输入特征图大小进行拼接，输入的特征图亦参与拼接，拼接后的结果作为整个Pyramid Pooling Module模块的输出。

网络最终使用卷积将Pyramid Pooling输出映射至目标softmax分类，预测结果与提取特征图的大小相同，为原始输入图片的1/8。

# 参考文献

{% blockquote %}

Zhao, H., Shi, J., Qi, X., Wang, X., & Jia, J. (2017). Pyramid scene parsing network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 2881-2890).

{% endblockquote %}
