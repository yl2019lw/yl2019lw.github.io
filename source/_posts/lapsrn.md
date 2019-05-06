---
title: LapSRN:Deep Laplacian Pyramid Networks for Fast and Accurate Super-Resolution
tags:
  - CVPR2017
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-06 20:35:48
mathjax: true
---

# 介绍

文章提出了LapSRN完成超分辨率任务。LapSRN使用原始低分辨率图片作为输入，渐进地预测多个尺度级别的高分辨率输出。在每个金字塔级别，模型使用粗糙的feature map作为输入，预测高频残差信息，并使用转置卷积上采样至细致级别。模型使用Charbonnier loss对异常值更加鲁棒。

# LapSRN

LapSRN网络结构如下图所示：

{% asset_img lapsrn.png lapsrn %}

模型使用低分辨率图片作为输入，渐进地预测$log_2S$级别的残差图像，其中$S$为scale factor。如图所示有3个级别，分别预测2倍，4倍，8倍分辨率的高清图像。网络包含特征提取与图像重构两个分支, 整个网络由一系列相似结构的部分层叠组成。

## Feature extraction

在级别s特征提取分支包含d层卷积及1层转置卷积，转置卷积将提取的feature map放大2倍，其输出送入两个不同的分支：1）一层卷积用来重构级别s处的残差图像；2）一层卷积用来提取级别s+1的特征。

## Image reconstruction

在级别s输入图像通过转置卷积放大2倍，转置卷积核通过双线性插值进行初始化并可随网络训练进行更新。上采样的图像再与来自特征提取分支预测的残差图像进行逐像素相加得到级别s预测的高清图像。级别s预测的高清图像作为级别s+1重构分支的输入。

## Loss 

模型根据输入$x$产生预测$\hat{y} = f(x;\theta)$。令在级别s处学习的残差图像为$r_s$，上采样的低分辨率图像为$x_s$，对应高分辨率ground truth为$y_s$，$y_s$由y进行bicubic下采样对应尺度获得。损失函数为：
\begin{equation}
    L(\hat{y}, y; \theta) = \frac{1}{N}\sum_{i=1}^{N}\sum_{s=1}^{L}\rho(y_s^{(i)} - x_s^{(i)} - r_s^{(i)})
\end{equation}
其中$\rho(x) = \sqrt{x^2 + \epsilon^2}$。

## Metric

在PSNR及SSIM之外，使用了IFC指标。（待说明）

# 参考文献
{% blockquote %}

Lai, W. S., Huang, J. B., Ahuja, N., & Yang, M. H. (2017). Deep laplacian pyramid networks for fast and accurate super-resolution. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 624-632).

{% endblockquote %}
