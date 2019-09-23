---
title: Soft-NMS - Improving Object Detection With One Line of Code
tags:
  - ICCV2017
  - detection
category:
  - computer vision
  - detection
  - misc
date: 2019-06-27 15:06:12
mathjax: true
---

# 介绍

在目标检测系统中通常会使用Non-maximum suppression来进行后处理。其首先将检测出的备选box根据分类score进行排序，拥有最高得分的box $M$加入输出结果并移除备选。

所有与$M$的IoU超过一定阈值的其它box均被丢弃，对剩下的所有备选box重复此过程直到所有box处理完毕，挑出的一系列拥有高分值的box作为检测系统的最终输出结果。

如果一个物体真实存在于一个相对得分低且IoU超过给定阈值的box，此处理过程将导致检测系统丢失该物体。

文章提出了Soft-NMS，在抑制过程中降低box的score而非直接暴力丢弃该box，使用了Soft-NMS的检测系统可获得稳定的性能提升。

# Soft NMS

Soft-NMS在处理时根据弱势box与高得分box的IoU降低其score，处理后弱势box仍然处于备选列表中，此时根据新的score进行排序后重复抑制过程。

如果一个弱势box与最高得分box有非常大的IoU，应将其score降低到非常小的值。如果弱势box与高得分box的IoU很小，则可以近似保留其原始score。

Soft NMS与NMS算法处理流程如下所示。

<div class='img-size-half'>
{% asset_img softnms.png Soft NMS %}
</div>

当前目标检测系统在多个IoU阈值上度量Average Precision，当使用较高的检测阈值$O_t$而使用较低的NMS阈值$N_t$时，容易导致有较高重叠的真实物体丢失。

当使用较低的检测阈值$O_t$并使用较高的NMS阈值$N_t$时，容易留下太多背景box造成false positive。

原始NMS处理相当于将被抑制的box的score直接设置成0，此为hard NMS。

\begin{equation}
    s_i = \begin{cases} s_i, & iou(M, b_i) < N_t, \\ 0, & iou(M, b_i) \ge N_t \end{cases}
\end{equation}

Soft-NMS根据弱势box与$M$的重叠程度降低其score，可采用如下线性衰减过程。

\begin{equation}
    s_i = \begin{cases} s_i, & iou(M, b_i) < N_t, \\ s_i(1-iou(M, b_i)), & iou(M, b_i) \ge N_t \end{cases}
\end{equation}

亦可采用相对IoU变化更加连续的高斯衰减过程。

\begin{equation}
    s_i = \begin{cases} s_i, & iou(M, b_i) < N_t, \\ s_i e^{-\frac{iou(M, b_i)^2}{\sigma}}, & iou(M, b_i) \ge N_t \end{cases}
\end{equation}

Soft NMS的计算复杂度与直接使用NMS相同。

# 参考文献

{% blockquote %}

Bodla, N., Singh, B., Chellappa, R., & Davis, L. S. (2017). Soft-NMS--Improving Object Detection With One Line of Code. In Proceedings of the IEEE International Conference on Computer Vision (pp. 5561-5569).

{% endblockquote %}
