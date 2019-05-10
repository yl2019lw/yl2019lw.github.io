---
title: DRRN:Image Super-Resolution via Deep Recursive Residual Network
tags:
  - CVPR2017
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-07 12:01:41
mathjax: true
---

# 介绍

文章提出DRRN，其采用全局与局部residual learning来解决训练深层网络的困难，同时使用recursive learning来控制增加模型深度时带来的参数量增加。DRRN使用更少的参数获取了SISR任务的最优性能。DRRN与其它相关模型对比如下图：

{% asset_img drrn.png drrn %}

# DRRN

## Residual Unit

在VDSR与DRCN中低分辨率输入与高分辨率输出的残差连接为global residual learning，DRRN亦采用全局连接。同时堆叠深层网络时会出现训练退化问题，文章亦可采用如resnet方式进行local residual learning。resnet每个残差单元使用不同的输入进行identity mapping，但DRRN内部残差单元均使用相同的输入,且残差单元以pre-activation方式实现。
\begin{equation}
    H^u = g(H^{u-1}) = F(H^{u-1}, W) + H^0
\end{equation}

## Recursive Block

文章使用Recursive Block方式在增加网络深度时控制参数数量，Recursive Block内可包含多个局部残差块，基本残差块包含两层卷积，所有残差块均使用相同的输入作为identity branch输入。同一Recursive Block内残差块参数是共享的，不同Recursive Block间参数不同。一个Recursive Block示意图如下：

{% img /drrn/recursive.png 400 recursive %}

令B为网络中Recursive Block数量，U为Recursive Block内残差单元数量，则有$H_b^0 = f_b(x_{b-1})$为$x_{b-1}$通过Recursive Block内第一个卷积后的输出。通过$u-th$残差单元后的结果为$H_b^u = g(H^{u-1}) = F(H_b^{u-1}, W_b) + H_b^0$，第b个Recursive Block的输出为$x_b = H_b^U = g^{(U)}(f_b(x_{b-1})) = g(g(\dots (g(f_b(x_{b-1})))\dots))$。

## Network Structure

DRRN通过堆叠一系列Recursive Block实现，最终使用1层卷积重构低分辨率输入与高分辨率输出之间的残差图像。因每个Recursive Block内部首先使用1层卷积再附加递归的残差单元，故网络深度$d = (1 + 2 \times U) \times B + 1$。如下图所示为取$B=6, U=3$的DRRN网络结构：

{% img /drrn/B6U3.png 400 B6U3 %}

当$U = 0$时，DRRN结构退化为VDSR。命$x,y$为DRRN的输入与输出，$R$为$b-th$Recursive Block映射函数，则有
\begin{equation}
    x_b = R_b(x_{b-1}) = g^{(U)}(f_b(x_{b-1})) \\
    y = f_{Rec}(R_B(R_{B-1}(\dots(R_1(x))\dots))) + x
\end{equation}
其中当$b = 1$时有$x_0 = x$，$f_{Rec}$为最终层卷积重构。DRRN使用MSE作为损失函数。

# 参考文献

{% blockquote %}

Tai, Y., Yang, J., & Liu, X. (2017). Image super-resolution via deep recursive residual network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 3147-3155).

{% endblockquote %}
