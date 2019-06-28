---
title: MNC:Instance-aware Semantic Segmentation via Multi-task Network Cascades
tags:
  - CVPR2016
  - segmentation
category:
  - computer vision
  - segmentation
  - instance segmentation
date: 2019-06-12 13:34:46
mathjax: true
---

# Introduction

文章提出MNC进行准确且快速地实例分割，其将实例分割拆解为differentiating instances，estimating masks，categorizing objects三个阶段性任务。

三个子任务共享卷积特征提取，后一阶段任务依赖前一阶段任务的输出，故称作Multi-task Network Cascades。

Differentiating instances输出类别无关的bounding box来分开不同的实例，estimating masks对每个实例预测其像素级前景/背景掩码，categorizing objects预测mask-level实例的类别。

MNC赢得MS COCO 2015分割任务第一名。

# Multi-task Network Cascades

MNC以任意大小的图像作为输入，输出instance-aware semantic segmentation results，网络示意图如下：

{% asset_img mnc.png Multi-task Network Cascades %}

## Regressing Box-level Instances

MNC第一阶段生成类别无关的bounding box并给出其objectness score，此过程采用Region Proposal Network实现。

RPN在共享的终层feature map每个位置处，与参考的一系列不同大小及面宽比的anchor进行box regression，即先通过3x3卷积reduce dimension，然后通过两分支1x1卷积预测box位置和是否为object。

其损失函数为:

\begin{equation}
    L_1 = L_1(B(\Theta))
\end{equation}
其中$B=\{B_i\}$为一系列bounding boxes，$B_i = \{x_i, y_i, w_i, h_i, p_i\}$标识每个box的位置、大小及objectness得分，$\Theta$为参数。

训练与推理过程中采用Non-maximum suppression丢弃与已知更高objectness score的box有较大IoU的重复boxes。

## Regressing Mask-level Instances

第二阶段以共享的特征图与第一阶段输出的bounding box作为输入，对每个box proposal输出类别无关的segmentation mask。

对每个box proposal，先采用RoI pooling对任意大小的box输入产生固定大小的feature，然后接两层全连接层进行pixel-level mask回归。

输出mask大小固定为$m \times m$，最终全连接层有$m^2$个输出，每个输出与ground truth mask进行binary logistic regression。

\begin{equation}
    L_2 = L_2(M(\Theta)|B(\Theta))
\end{equation}
其中$M = \{M_i\}$为一系列box对应的前景/背景mask，$L_2$依赖于第一阶段产生的box。

相较于DeepMask，MNC仅针对少量的box proposal产生segmentation mask。

MNC中RoI pooling反向传播中依赖于box位置，因此实现时先通过differentiable RoI warping至固定大小再执行标准max-pooling。

## Categorizing Instances

第三阶段以共享的特征，第一阶段输出boudning box，第二阶段输出的mask作为输入，输出每个实例的类别。

对第一阶段产生的box，同样先通过RoI pooling提取固定大小特征，然后该特征被第二阶段产生的mask进行masked，使得可只关注前景特征。
\begin{equation}
    F_i^{Mask}(\Theta) = F_i^{RoI}(\Theta) \cdot M_i(\Theta)
\end{equation}

对$F_i^{Mask}$后跟两层4096-d全连接层生成分类特征，此为mask-based pathway。同时亦直接采用$F_i^{RoI}$跟全连接层生成分类特征，此为box-based pathway。

box-based pathway可防止有效特征全部被masked丢失，此二者进行拼接后输入$N+1$ 路softmax进行分类，损失函数如下：

\begin{equation}
    L_3 = L_3(C(\Theta)|B(\Theta), M(\Theta))
\end{equation}

全部Loss为三部分Loss之和。

## Cascades with More Stages

MNC可使用更多阶段进行迭代式训练。如在第三阶段分类时同时生成class-wise bouding box，将其可看作第一阶段的box proposal，第四阶段与第五阶段采用同第二、三阶段的结构。

<div class='img-size-half'>
{% asset_img stage.png stage %}
</div>

# 参考文献

{% blockquote %}

Dai, J., He, K., & Sun, J. (2016). Instance-aware semantic segmentation via multi-task network cascades. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 3150-3158).

{% endblockquote %}
