---
title: RetinaNet:Focal Loss for Dense Object Detection
tags:
  - ICCV2017
  - detection
category:
  - computer vision
  - detection
  - one-stage detector
date: 2019-06-27 09:24:37
mathjax: true
---

# 介绍

文章分析了当前单阶段检测器精度低于两阶段检测器性能的原因，认为两阶段检测器中的region proposal过程抑制了大量负样本，在训练时使用固定正负样本比或OHEM来采样，缓解了样本不均衡问题。

单阶段检测器在所有位置密集地预测bounding box及分类score，其中大量位置处没有物体，正负样本不均衡问题十分严重。

文章提出了Focal Loss来解决分类中的样本不均衡问题，对于大量容易被分类的负样本动态地降低其权重, 同时基于Focal Loss构建了单阶段检测器RetinaNet，在保持速度优势的同时超过了两阶段检测器的性能。

# Focal Loss

通常分类任务使用交叉熵损失函数：
\begin{equation}
    CE(p, y) = \lbrace \begin{array}{l} -log(p) \qquad if \quad y = 1 \\ -log(1-p) \quad otherwise \end{array}
\end{equation}

其中$y \in \lbrace \pm 1 \rbrace$标记真实类别，p为预测概率，可简化标记$p_t$。

\begin{equation}
    p_t = \lbrace \begin{array}{l} p \qquad if \quad y = 1 \\ 1-p \quad otherwise \end{array}
\end{equation}

此时交叉熵可重写为$CE(p, y) = CE(p_t) = -log(p_t)$。

通常在处理样本不均衡问题时可给予不同类别的损失不同的权重，此权重可设置为与类别出现频次成反比或通过交叉验证确定。

对class 1权重因子$\alpha$, class -1权重因子$1 - \alpha$，其中$\alpha \in [0, 1]$，可同样简写为$\alpha_t$，此时有$\alpha$-balanced损失为:

\begin{equation}
    CE(p_t) = -\alpha_t log(p_t)
\end{equation}

容易被分类的负样本因数量庞大占据了loss主要部分进而主导参数更新，$\alpha$-balanced损失并未对难易样本进行区分。Focal Loss定义如下：

\begin{equation}
    FL(p_t) = -(1-p_t)^\gamma log(p_t)
\end{equation}

不同$\gamma$值设定的Focal loss如下图所示。

<div class='img-size-half'>
{% asset_img focal-loss.png Focal Loss %}
</div>

当一个样本难以被分类时，其$p_t$值较小，调制因子接近于1，损失函数相当于原始交叉熵。简单样本的$p_t$值接近于1，调制因子接近于0，导致容易样本被大幅降权。

显见Focal loss减少了容易样本对loss的贡献并且扩展了样本处于低loss的范围。实践中可采用$\alpha$-balanced版本的Focal loss。

\begin{equation}
    FL(p_t) = -\alpha_t(1-p_t)^\gamma log(p_t)
\end{equation}

# RetinaNet

目标检测需要输出bounding box及class score，文章采用Focal Loss作为分类子任务的损失函数构建了单阶段检测器RetinaNet。

RetinaNet采用ResNet作为骨干网络，利用特征金字塔FPN融合多尺度特征，最终在不同的pyramid level预测bounding box及class score。

{% asset_img retinanet.png RetinaNet %}

在特征金字塔的每个level为A个anchor预测K个类别score及4个bounding box坐标，因此分类分支与定位分支通过一系列$3 \times 3$卷积后最终分别预测KA及4A个channel结果。

Focal loss被用于分类分支的全部anchor而非Faster R-CNN中部分采样, 其可有效地抑制大量的容易样本对loss的贡献。

# 参考文献

{% blockquote %}

Lin, T. Y., Goyal, P., Girshick, R., He, K., & Dollár, P. (2017). Focal loss for dense object detection. In Proceedings of the IEEE international conference on computer vision (pp. 2980-2988).

{% endblockquote %}
