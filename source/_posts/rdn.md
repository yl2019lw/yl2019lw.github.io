---
title: RDN:Residual Dense Network for Image Super-Resolution
tags:
  - CVPR2018
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-08 22:37:29
mathjax: true
---

# 介绍

本文提出了Residual Dense Network完成超分任务，其采用设计的Residual Dense Block从原始低分辨率输入中提取层次化地特征作为RDN的组成元件。

RDB包含密集连接层，局部特征融合与局部残差学习。RDB支持contiguous memory机制，前一个RDB的输出作为下一个RDB内所有节点的输入。
RDN内部的节点将其输出送入同一RDB内所有的后续节点，能够有效地保留需要传递的信息。

局部特征融合通过拼接前一RDB的输出与当前RDB内所有卷积节点的输出作为其输入，能够自适应地保留关键信息，同时局部特征融合允许较高的growth rate。
获得多个层次的RDB输出的局部特征后，通过全局特征融合可以用全局地方式保留层次化的信息。
RDN更有效地综合利用了层次化的特征信息，其与MDSR，SRDenseNet使用的特征提取块对比如下图：

<div class='img-size-half'>
{% asset_img comparison.png comparison %}
</div>

# Residual Dense Network

## Network Structure

RDN网络包含shallow feature extraction net(SFENet)，residual dense blocks(RDB)，dense feature fusion(DFF)，up-sampling net(UPNet)共四个部分，模型结构如下图所示：

{% asset_img rdn.png rdn%}

令$I_{LR}, I_{SR}$分别的RDN网络的输入与输出。浅层特征提取网络使用两层卷积，首层为：
\begin{equation}
    F_{-1} = H_{SFE1}(I_{LR})
\end{equation}
其中输出$F_{-1}$用于全局残差学习，也用于浅层特征提取中第二层的输入：
\begin{equation}
    F_0 = H_{SFE2}(F_{-1})
\end{equation}
其中$F_0$作为residual dense blocks的输入。假定有$D$个RDB，第$d$个RDB的输入$F_d$为：
\begin{equation}
    F_d = H_{RDB,d}(F_{d-1}) = H_{RDB,d}(H_{RDB,d-1}(\cdots(H_{RDB,1}(F_0))\cdots))
\end{equation}
其中$H_{RDB,d}$表示RDB内第$d$个结点的操作，即卷积与relu，$F_d$块作为整体RDB的输出可看作网络中的局部特征。

当获取一系列RDBs的层次化特征后，进行包含全局特征融合与全局残差学习的dense feature fusion，其利用前面所有层的输出：
\begin{equation}
    F_{DF} = H_{DFF}(F_{-1}, F_0, F_1, \cdots, F_D)
\end{equation}

最终将在low resolution space提取出的特征送入up-sampling net获得重构的高分辨率预测，上采样网络采用ESPCN方式实现：
\begin{equation}
    I_{SR} = H_{RDN}(I_{LR})
\end{equation}

## Residual Dense Block

RDB包含dense connected layers, local feature fusion, local residual learning实现了contiguous memory机制，如下图所示：

<div class='img-size-half'>
{% asset_img rdb.png rdb %}
</div>

### Contiguous memory

通过将前一个RDB的输出输入至当前RDB的所有节点实现contiguous memory机制。所有RDB输出$G_0$个feature maps，$F_{d-1},F_{d}$是第$d$个RDB的输入与输出，则在第$d$个RDB内第$c$个卷积层有：
\begin{equation}
    F_{d,c} = \sigma(W_{d,c}[F_{d-1}, F_{d,1},\cdots,F_{d,c-1}])
\end{equation}
假定$F_{d,c}$有$G$个feature maps，则其输入有$G_0 + (c-1) \times G$个feature maps。

### Local feature fusion

局部特征融合处理前一个RDB的输出与当前RDB内所有结点的输出，其通过$1 \times 1$卷积来输出信息$F_{d,LF}$：
\begin{equation}
    F_{d, LF} = H_{LFF}^d([F_{d-1}, F_{d,1}, \cdots, F_{d,c}, \cdots, F_{d,C}])
\end{equation}

### Local residual learning

局部残差学习将当前RDB局部特征融合的输出与其输入相加得到当前RDB的特征$F_d$：
\begin{equation}
    F_d = F_{d-1} + F_{d, LF}
\end{equation}

## Dense Feature Fusion

在通过一系列RDBs提取到局部密集特征后，以全局地方式融合层次化的特征，DFF包含global feature fusion以及global residual learning。

### Global feature fusion

使用所有RDBs输出特征进行融合可得全局融合特征$F_{GF}$：
\begin{equation}
    F_{GF} = H_{GFF}([F_1, \cdots, F_D])
\end{equation}
其中$H_{GFF}$为$1 \times 1$卷积后接$3 \times 3$卷积。

### Global residual learning

全局融合特征与浅层提取特征相加可得全局特征$F_{DF}$:
\begin{equation}
    F_{DF} = F_{-1} + F_{GF}
\end{equation}

# Discussions

## Difference to DenseNet

与DenseNet相比，RDN专为超分任务设计。其不包含batch normalization，否则占用同卷积层相当的显存还妨碍性能。也不包含pooling层，否则会丢失pixel-level信息。亦没有DenseNet相邻block中间的过渡层。在RDN中使用局部特征融合，将前一个块的输出作为后一个块内所有节点的输入。另外还使用了全局特征融合来充分使用层次化的特征。

## Difference to SRDenseNet

RDN引入contiguous memory机制，允许前一RDB信息直接访问至当前RDB内所有节点。RDB使用局部特征融合后允许密集连接使用更大的growth rate，能够稳定训练较宽的网络。RDB使用局部残差学习带来更好地信息流动与梯度反传。另外RDN使用L1损失函数而非L2。

## Difference to MemNet

MemNet需要使用低分辨率图片插值到高分辨率空间作为输入会带来计算复杂度，而RDN直接使用低分辨率输入。MemNet中的块包含recursive unit及gate unit，但其并不接受前面块或当前块之前节点的信息作为输入。RDN能够有效地在低分辨率空间充分利用层次化特征信息。

# 参考文献

{% blockquote %}

Zhang, Y., Tian, Y., Kong, Y., Zhong, B., & Fu, Y. (2018). Residual dense network for image super-resolution. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2472-2481).

{% endblockquote %}
