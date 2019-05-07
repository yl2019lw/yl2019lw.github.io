---
title: MemNet:A Persistent Memory Network for Image Restoration
tags:
  - ICCV2017
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-07 21:18:17
mathjax: true
---

# 介绍
文章提出MemNet来解决image denosing, super-resolution, JPEG deblocking任务。对于层数较深的神经网络有突出的long-term dependency问题，文章引入包含recursive unit及gate unit的memmory block，通过自适应的学习过程挖掘memory。Memory block内recursive unit学习在当前状态下不同感受野大小的不同级别的特征表示作为short-term memory，与之前的memory block的输出即long-term memory拼接在一起作为当前memory block内gate unit的输入。Gate unit自适应地控制有多少long-term memory被保留及多少当前的short-memory被保存。Memory block示意图如下：

{% img /2019/05/07/memnet/memblock.png 400 memblock %}

# MemNet
## Basic Network Architecture

MemNet包含三部分：特征提取网络FENet，多层堆叠的Memory block，重构网络ReconNet。如下图所示：

{% asset_img memnet.png memnet %}

FeNet使用一层卷积从低分辨率图片提取特征。令x为输入，y为输出，$f_{ext}$为特征提取网络，$B_0$作为提出的特征输入堆叠的Memory block，则有：
\begin{equation}
    B_0 = f_{ext}(x)
\end{equation}

堆叠$M$个memory block作用如下：
\begin{equation}
    B_m = M_m(B_{m-1}) = M_m(M_{m-1}(\dots(M_1(B_0))\dots))
\end{equation}
其中$M_m$为第m个memory block的映射函数，$B_{m-1}$及$B_m$分别为其输入与输出。

最终使用ReconNet学习低分辨率输入到高分辨率输出之间的残差图像：
\begin{equation}
    y = D(x) = f_{rec}(M_M(M_{M-1}(\dots(M_1(f_{ext}(x)))\dots))) + x
\end{equation}

给定N个训练样本，$x,\hat{x}$为低分辨率及高分辨率ground truth样本对，则loss函数为：
\begin{equation}
    L(\theta) = \frac{1}{2N}\sum_{i=1}^{N}||\hat{x}^{(i)} - D(x^{(i)})||^2
\end{equation}

## Memory block

网络中部为堆叠的memory block, 每个memory block包含多个recursive unit节点及一个gate unit。

### Recursive Unit

Recursive unit为递归的residual building block，第m个memory block内的一个残差块可表示为：
\begin{equation}
    H_m^r = R_m(H_m^{r-1}) = F(H_m^{r-1}, W_m) + H_m^{r-1}
\end{equation}
其中$H_m^{r-1},H_m^r$为第m个memory block内第r个残差块的输入与输出，R表示残差映射函数，当$r=1$时有$H_m^0 = B_{m-1}$。每个残差块为pre-activation方式的两层卷积:
\begin{equation}
    F(H_m^{r-1}, W_m) = W_m^2\gamma(W_m^1\gamma(H_m^{r-1}))
\end{equation}
其中$\gamma$表示batch norm及relu。

多个残差块作为recursive unit节点学习不同级别地特征表示，其参数被共享，这些特征表示是此memory block内short-term memory。如在recursive unit中有$R$个recursions，则第$r$个可表示为：\begin{equation}
    H_m^r = R_m^{(r)}(B_{m-1}) = R_m(R_m(\dots(R_m(B_{m-1}))\dots))
\end{equation}
其中共有$r$个$R_m$，${H_m^r}_{r=1}^R$为recursive unit的多个级别特征表示，short-term memory为$B_m^{short} = [H_m^1, H_m^2,\dots,H_m^R]$，前面多个memory block的输出拼接在一起组成long-term memory为$B_m^{long} = [B_0, B_1,\dots,B_{m-1}]$，short-term及long-term memory拼接在一起作为gate unit的输入：
\begin{equation}
    B_m^{gate} = [B_m^{short}, B_m^{long}]
\end{equation}

### Gate Unit

Gate unit采用$1 \times 1$卷积为不同的memories学习自适应的权重并给出第m个memory block整体输出:
\begin{equation}
    B_m = f_m^{gate}(B_m^{gate}) = W_m^{gate}\gamma(B_m^{gate})
\end{equation}
从整体来看，形式化地有：
\begin{equation}
    B_m = M_m(B_{m-1}) = f_{gate}([R_m(B_{m-1}),\dots,R_m^{(R)}(B_{m-1}),B_0,\dots,B_{m-1}])
\end{equation}

## Multi-Supervised Memnet

同DRCN类似，可以把中间memory block的输出作为ReconNet的输入获取共$M$个预测，将这些预测进行加权综合得最终预测,示意图如下：

{% asset_img multi-supervise.png multi-supervised %}

对第m个memory block的输出构建的预测为：
\begin{equation}
    y_m = \hat{f}_{rec}(x, B_m) = x + f_{rec}(B_m)
\end{equation}
加权输出为$y = \sum_{m=1}^{M} w_m \cdot y_m$，${ w_m }_{m=1}^{M}$为学习的权重，损失函数为中间memory block重构预测与综合预测两部分损失的平衡：
\begin{equation}
    L(\theta) = \frac{\alpha}{2N}\sum_{i=1}{N}||\hat{x}^{(i)} - \sum_{m=1}^{M} w_m \cdot y_m^{(i)}||^2 + \frac{1 - \alpha}{2MN}\sum_{m=1}{M}\sum_{i=1}^{N}||\hat{x}^{(i)} - y_m^{(i)}||^2
\end{equation}

# 参考文献

{% blockquote %}

Tai, Y., Yang, J., Liu, X., & Xu, C. (2017). Memnet: A persistent memory network for image restoration. In Proceedings of the IEEE international conference on computer vision (pp. 4539-4547).

{% endblockquote %}
