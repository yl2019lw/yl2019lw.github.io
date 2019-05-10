---
title: SENet:Squeeze-and-Excitation Networks
date: 2019-03-24 12:00:02
tags:
  - CVPR2018
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍

文章聚焦在channel之间特征相关性来提升深度网络的表达能力，提出了Squeeze-and-Excitation block来对不同channel的特征进行重标定。文章使用SENet结构获得了ILSVRC-2017分类任务的冠军，在ImageNet数据集上top-5 error低至2.251%。

基本的SE block如下所示：

{% asset_img seblock.png squeeze excitation block %}
 
对于网络中的变换$F_{tr}: X \to U, X \in \mathbb{R}^{H' \times W' \times C'}, U \in \mathbb{R}^{H \times W \times C}$，我们可以构造SE block来进行特征标定。首先把特征$U$通过一个squeeze操作，即把feature map在$H \times W$上进行聚合得到一个channel descriptor。此channel
descriptor包含了channel-wise特征响应的全局分布，能够使得全局感受野的信息被网络的低层所利用。然后执行excitation操作，对每个channel通过self-gating机制生成权重，最终使用不同channel的权重对原始U进行处理，把得到的结果替代U作为后续层的输入。

SENet可由许多SE Block堆叠而成，SE Block可以在原始网络的任意深度处作就地替换。在浅层中SE Block更多了excite类别无关信息，在深层中逐渐特化。

# Squeeze-and-Excitation Blocks

对于网络中的变换$F_{tr}: X \to U, X \in \mathbb{R}^{H' \times W' \times C'}, U \in \mathbb{R}^{H \times W \times C}$，我们简化$F_{tr}$为一系列卷积操作。命$V = [v_1, v_2, ...v_C]$表示对应的卷积核，$F_{tr}$的输出$U = [u_1, u_2, ...u_C]$，其中
\begin{equation}
    u_c = v_c * X = \sum_{s=1}^{C'} v_c^s * x^s
\end{equation}
其中*表示卷积操作，$v_c = [v_c^1, v_c^2, ..., v_c^{C'}], X = [x^1,
x^2,...,x^{C'}]$。由上式可见输出是由输入的每一个channel处都参与summation得到,因此已隐含有channel依赖信息，但此信息与空间位置混在一起。文章的目的在于提升网络对于有用特征的灵敏度，同时抑制无用信息，通过squeeze和excitation操作来显式地建模不同通道特征之间相关性，进而进行特征标定。

* Squeeze: Global Information Embedding

    由于原卷积操作只作用于局部感受野，不能利用全局上下文信息，因此首先使用squeeze操作来将全局位置信息编码入channel descriptor。这是通过全局平均池化的方式来得到$z \in \mathbb{R}^C$，其中第c个元素为：
\begin{equation}
    z_c = F_{sq}(u_c) = \frac{1}{H \times W} \sum_{i=1}{H}\sum{j=1}{W}u_c(i, j)
\end{equation}

* Excitation: Adaptive Recalibration

    然后使用excitation捕获channel-wise依赖：
\begin{equation}
    s = F_{ex}(z, W) = \sigma(g(z, W)) = \sigma(W_2\sigma(W_1z))
\end{equation}
其中$\sigma$表示Relu函数，$W_1 \in \mathbb{R}^{\frac{C}{r} \times C}, W_2 \in \mathbb{R}^{C \times \frac{C}{r}}$,即通过两个全连接层来学习非线性权重，其中一个以比率$r$降低维度，另一个进行还原。最终标定后输出为：
\begin{equation}
    \widetilde{x}_c = F_{scale}(u_c, s_c) = s_c \cdot u_c
\end{equation}
其中$\widetilde{X} = [\widetilde{x}_1, \widetilde{x}_2, ..., \widetilde{x}_C]$, $F_{scale}(u_c, s_c)$为在特征图$u_c \in \mathbb{R}^{H \times W}$与标量$s_c$之间channel-wise乘法。

SENet可由SE Block对原网络结构改造而成，如ResNet残差单元及Inception模块修改后基本结构如下：

{% img /senet/schema.png schema 400 %}

# SENet

SE-ResNet-50及SE-ResNext-50整体组成单元见下表：

{% asset_img se-resnet.png  se resnet %}

可见仅在原残差块内增加2个全连接层,额外引入参数数量为：
\begin{equation}
    \frac{2}{r}\sum_{s=1}^{S}N_s \cdot C_s^2
\end{equation}
其中r为reduction ratio，$S$为不同feature map大小的阶段数目，$C_s$为第$s$个阶段的输入特征通道维度，$N_s$为每个阶段的重复残差块数量。

在ImageNet数据集上运行结果如下：

{% asset_img imagenet.png imagenet %}

# 参考文献

{% blockquote %}

Hu, J., Shen, L., & Sun, G. (2018). Squeeze-and-excitation networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 7132-7141).

{% endblockquote %}
