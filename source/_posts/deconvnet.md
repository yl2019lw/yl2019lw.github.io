---
title: DeconvNet:Learning Deconvolution Network for Semantic Segmentation
tags:
  - ICCV2015 
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-04-25 21:56:38
mathjax: true
---

# 介绍
文章提出deep deconvolution network进行图像语义分割，deconvolution network包含了deconv层以及unpooling层。文章使用基于proposal的方式训练语义分割网络，并将输入图片的全部proposal的分割结果进行合并得到最终结果。
全卷积网络使用整张图片作为输入进行语义分割，其有固定的感受野大小，过大的物体容易被fragmented，过小的物体容易mislabeled。同时，全卷积网络中在较粗糙的map上仅进行一次deconv，细节信息易丢失或被平滑。
DeconvNet在上采样过程中使用一系列堆叠的doconvolution, unpooling, relu层，基于proposal的训练方式使其解除了感受野不受物体大小的限制，在当时获得VOC 2012上state-of-the-art。

# DeconvNet
DeconvNet包含下采样卷积网络特征提取部分以及上采样信息还原两部分，示意图如下：

{% asset_img deconv.png deconv %}

其中Convolution network为VGG16去掉全连接层，额外两个1x1全卷积， Deconvolution network包含unpooling, deconvolution及relu。网络大致是对称结构，unpooling即在相对应的pooling层记住最大池化选取结果的位置，将当前activation的结果放到该位置处，其它位置为0。Deconv相对conv为转置卷积，根据一个输入获得多个输出。示意图如下:

{% img /deconvnet/unpooling.png 500 unpooling %}

# Experiments
文章进行两阶段训练。第一阶段根据gound truth标注去框选物体作为proposal输入网络进行训练，第二阶段使用edgeboxes为每张图片生成proposal作为训练输入。

对每个proposal输出预测$g_i \in R^{H \times W \times C}$,需要将其贴回原图对应位置，proposal以外的贴0，获得输出$G_i$，最终将多个proposal的结果合并作为整张图片的分割结果。对每个位置每个类别有$P(x,y,c) = max_i G_i(x, y, c), \forall i$，在每个位置处使用softmax将每个类别的结果归一化为概率分布。

最终输出结果通过crf进行refinement有性能提升。模型与FCN进行集成有较大性能提升，集成方式为将两种方法输出的score map进行平均。

# 参考文献
{% blockquote %}

Noh, H., Hong, S., & Han, B. (2015). Learning deconvolution network for semantic segmentation. In Proceedings of the IEEE international conference on computer vision (pp. 1520-1528).

{% endblockquote %}
