---
title: Faster-RCNN:Towards Real-Time Object Detection with Region Proposal Networks
tags:
  - NIPS2015
  - detection
category:
  - computer vision
  - detection
date: 2019-04-10 21:55:11
mathjax: true
---

# 介绍
在Fast R-CNN之后，使用selective search或edge boxes等方式产生region proposal的过程成为了目标检测问题中的时间瓶颈。文章提出了Faster R-CNN模型，即复用提取的网络特征来通过region proposal network产生候选的目标位置框，大幅加快了目标检测的速度，同时也将mAP指标在Voc2007上提高至73.2%，Voc2012上为70.4%。

# Region Proposal Network

## RPN
通过卷积提取的feature map，不仅可用于Fast R-CNN中的检测任务，也可用于生成Region Proposal。RPN网络构建于最终层feature map之上，包含两层卷积。其在最终feature map上的每一个位置进行滑动，也就是密集地在每一个可能的位置去试探生成不同大小与长宽比的region proposal。第一层卷积使用$n \times n$大小的窗口密集地连接，如$3 \times 3$，生成特定大小的特征向量，对于ZFNet为256-d,VGG为512-d(对应最终层feature map通道数)，第二层包含两支$1 \times 1$卷积为每个可能的proposal生成objectness score和regressed bounds，每个feature map position生成不同大小与长宽比的K个proposal，这些预制的box称为anchor，这样第二层卷积共输出2k个是否为物体的得分和4K个位置数据进行回归。第一层使用3 x 3卷积就有较大的感受野。在所有位置处滑动的卷积参数是共享的。RPN示意图如下：

{% asset_img rpn.png region proposal network %}

## Translation-Invariant Anchors
在feature map上的每个位置，同时预测k个region proposal。每个proposal包含是否为物体的2个得分以及中心点位置与宽高的4个数值。坐标数值是相对于预定义的anchor的，每个anchor以当前滑动的feature map所在位置为中心，按一定的大小与长宽比进行定义。文章选取了3种尺度大小和3种长宽比，即在每个位置产生9个anchor。对于大小为$W \times H$的feature map，将一共产生$WHK$个anchor。实验中选取anchor面积大小近似为$128^2, 256^2, 512^2$，选取的三种长宽比为$1:1, 1:2, 2:1$。显然生成的anchor会有大量重叠，文章采用non-maximum suppression，根据每个anchore的objectness得分抑制掉那些有较大IoU的相对低objectness得分的proposal。

## Loss for Region Proposal
为训练RPN，对每个anchor赋予二值分类标记。对于同groud truth box有最大IoU的anchor被划分为正样本，与任意ground truth box有IoU大于0.7的anchor也被指定为正样本。对于同一个ground truth box，其可能为多个不同的anchor提供正样本标记。对于与所有ground truth box的IoU都小于0.3的anchor被认为是负样本，即不是正样本也不是负样本的anchore不参与训练过程。多任务损失函数如下：
\begin{equation}
    L(\{p_i\}, \{t_i\}) = \frac{1}{N_{cls}}\sum_i L_{cls}(p_i, p_i^*) + \lambda \frac{1}{N_{reg}}\sum_i p_i^*L_{reg}(t_i, t_i^*)
\end{equation}
其中$p_i$为第$i$个anchor为前景物体的概率，$t_i$为预测bounding box坐标数据，$p_i^*$与$t_i^*$为第$i$个anchor的ground truth。$L_{cls}$与$L_{reg}$与Fast R-CNN中定义相同，回归部分只针对正样本计算损失。回归部分为消除box大小带来的影响使用相对数值如下：
\begin{equation}
    t_x = (x - x_a)/w_a, t_y = (y - y_a)/w_a, t_w = log(w/w_a), t_h = log(h/h_a), \\
    t_x^* = (x^* - x_a)/w_a, t_y^* = (y^* - y_a)/w_a, t_w^* = log(w^*/w_a), t_h^* = log(h^*/h_a)
\end{equation}
不同于Fast R-CNN，文章所采用的k个anchor间不共享regressor参数。

# Results
Faster R-CNN大幅提升了速度，参见下表：

{% asset_img time.png time %}

# 参考文献
{% blockquote %}

Ren, S., He, K., Girshick, R., & Sun, J. (2015). Faster r-cnn: Towards real-time object detection with region proposal networks. Advances in neural information processing systems, 91-99.

{% endblockquote %}
