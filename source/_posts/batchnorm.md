---
title: Batch Normalization:Accelerating Deep Neural Network Training by Reducing Internal Covariate Shift
tags:
  - ICML2015
  - classification
category:
  - computer vision
  - classification
mathjax: true
date: 2019-03-21 14:30:15
---

# 介绍
文章提出Batch Normalization，解决了网络中每层处理单元的输入分布随参数更新变化的问题，也即internal covariate shift，使得能够使用较大的学习率，不用过多关注初始化，减少了使用dropout的必要，能够加快网络的训练和获得更好的性能。

在训练神经网络时，浅层参数的更新会影响后续层的输入，导致后续层需要适应其输入分布的变化。对于sigmoid这类饱和激活函数而言，当其输入数据落入其饱和区时会导致梯度接近于0，参数更新变小，影响网络收敛速度。

通常我们使用白化操作对输入数据进行预处理，主要是PCA白化和ZCA白化，通过白化操作会去除特征之间的相关性，使得输入分布有相同的均值和方差，但对网络中的每一层数据都进行白化操作成本太高，因此提出了batch normalization，使得网络中每一层处理单元的输入有相同的分布。

# Batch Normalization

Batch normalization对同一batch内网络中的每一层的每一个特征，首先进行归一化至均值为0，标准差为1，对于d维输入$x = (x^{(1)}, x^{(2)}, ..., x^{(d)}) $ 有：

<center>
${\hat{x}}^{(k)} = \frac{ x^{(k)} - E[x^{(k)}] }{\sqrt{Var[x^{(k)}]}}$
</center>

然后还原数据的表达能力：

<center>
${\hat{y}}^{(k)} = \gamma^{(k)} \hat{x}^{(k)} + \beta^{(k)}$
</center>
其中$\gamma^{(k)},\beta^{(k)}$是第k处需要学习的一对参数。经过上述处理后，对应k处数据有稳定的均值为$\beta^{(k)}$，标准差为$\gamma^{(k)}$的分布。
最终将网络中每一层中原始的数据$x^{(k)}$替换为$y^{(k)}$。算法描述见下图：

<div class='img-size-half'>
{% asset_img batch_norm_single.png batch norm single %}
</div>

对于全连接层，针对每个激活的神经元进行处理。对于卷积层，针对每一个feature map学习一对$\beta^{(k)}$，$\gamma^{(k)}$。

在测试时，网络中每一层的输入分布已经固定，其均值与标准差通过moving average的方式由训练时求得的$\beta^{(k)}$，$\gamma^{(k)}$计算而来，整体流程如下：

<div class='img-size-half'>
{% asset_img batch_norm_all.png batch norm all %}
</div>

# 总结
Batch Normalization加快了训练速度，能够获取更好的性能。BN-Inception是增加了batch normaliztion的inception模型，也即使Inception-V2，在ImageNet上获得了top-5 error为4.82%。

# 参考文献
{% blockquote %}

Ioffe, S., & Szegedy, C. (2015). Batch normalization: Accelerating deep network training by reducing internal covariate shift. arXiv preprint arXiv:1502.03167.

{% endblockquote %}
