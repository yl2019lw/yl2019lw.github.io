---
title: Fast R-CNN
tags:
  - ICCV2015 
  - detection
category:
  - computer vision
  - detection
  - two-stage detector
date: 2019-04-09 22:03:22
mathjax: true
---

# 介绍

文章基于R-CNN和SPP-net的改进提出了Fast R-CNN，大幅提高了检测速度，因此是Fast R-CNN。相较于之前的方法，提高了检测质量mAP，多任务方式训练同时处理proposal分类与位置校正，无需SVM可在训练中直接更新网络中所有参数，没有额外的空间需求来保存提取出来的特征。

# Fast R-CNN

## Architecture

Fast R-CNN把整张图片与及对应的region proposals一起作为输入。首先使用一系列卷积和池化层产生整张图片的feature map，然后对每个region proposal使用ROI pooling从feature map对应位置处提取出固定大小的特征表示。每个特征向量输入全连接层最终至分类与检测两个分支，其中分类分支通过softmax产生k+1个概率输出（K个类别，1背景），检测分支产生4K个输出进行回归（每个类4个值标记中心点及宽高）。整体处理流程如下：

<div class='img-size-half'>
{% asset_img architecture.png fast r-cnn %}
</div>

## RoI pooling

RoI即Region of Interest，对应到feature map上一个子区域，通过RoI pooling获得固定维度的特征表示。如将$h \times w$大小的RoI池化至$H \times W$大小的格子，则每个格子的池化窗口大小为$h/H \times w/W$。池化操作在每个不同的channel上独立进行，显然RoI pooling可看作是Spatial Pyramid Pooling只取一个pyramid level的特殊情况。当文章中采用Vgg16来作为backbone网络提取特征时，H = W = 7。

## Fine-tuning for detection

Spp-net不能较好地更新SPP以下的层的参数是因为其训练样本来自于许多不同的图片，当样本所用RoI的感受野较大时相当于要处理整张图片，这导致了很低的效率。文章采用了hierarchical sampling方式，即为得到R个样本，先选取N张图片，再从每张图片取R/N个RoI，如此来自于同一张图片的RoI可共享计算和内存。同时，因不再需要SVM进行分类，softmax分类及bounding box回归可同时进行训练。

### Multi-task loss

Fast R-CNN网络包含两个分支，其中分类分支对每个RoI输出K+1个类别概率$p = (p_0, p_1, ... p_K)$，回归分支对每个类输出bounding box回归$t^k = (t_x^k, t_y^k, t_w^k, t_h^k)$，$t^k$指明了相对于一个proposal的中心位置偏移及log-space的宽高偏差。每个训练样本RoI都有分类标签$u$和bounding box标签$v$，则多任务损失函数为：
\begin{equation}
    L(p, u, t^u, v) = L_{cls}(p, u) + \lambda[u \ge 1] L_{loc}(t^u, v)
\end{equation}
其中分类损失$L_{cls}(p, u) = -log p_u$， u = 0时为背景不参与回归部分计算。回归损失为：
\begin{equation}
    L_{loc}(t^u, v) = \sum_{i \in \{x,y,w,h\}} smooth_{L1} (t_i^u - v_i),
\end{equation}
其中：
\begin{equation}
    smooth_{L1}(x) = 0.5x^2 if |x| \le 1; |x| - 0.5 \quad otherwise.
\end{equation}

### Mini-batch sampling

训练中每个batch选取128个RoI，来自两张图片，每个图片采样64个RoI。其中有25%的样本为与ground truth box的IoU > 0.5的正样本，其余为IoU为(0.1,0.5)之间的负样本。

### Back-propagation

RoI pooling反向传播时仅对max pooling过程中选中的位置进行梯度传递。

## Faster detection

在检测任务中需要对所有RoI进行全连接的计算。为加速运算，可通过SVD分解使用两层较小的权重替代较大的权重参数。如对一层$u \times v$的权重$W$矩阵有$W = U \Sigma_t V^T$，则可截取前t大的奇异值，分解为使用一层为U参数量为$u \times t$，另一层为$\Sigma_t V^T$参数量为$v \times t$，则网络得到压缩。

# Results

在Voc2007运行结果见下表：
{% asset_img result.png result %}

# 参考文献

{% blockquote %}

Girshick, R. (2015). Fast r-cnn. In Proceedings of the IEEE international conference on computer vision (pp. 1440-1448).

{% endblockquote %}
