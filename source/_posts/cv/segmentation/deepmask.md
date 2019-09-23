---
title: DeepMask:Learning to Segment Object Candidates
tags:
  - NIPS2015
  - segmentation
category:
  - computer vision
  - segmentation
  - instance segmentation
date: 2019-06-11 18:34:43
---

# Introduction

文章提出DeepMask来生成用于分割的region proposal，其根据输入的image patch获得两种输出，一为类无关的segmentation mask，另为该patch完全包含一物体在其中部的得分。

相较于其他的region proposal方法，DeepMask使用较少的proposal就可获得更高的recall。DeepMask亦可泛化至为没见过的物体类别生成mask proposal。

DeepMask可直接从原始像素通过学习来生成segmentation proposal，不需要依赖于edges，super pixels或其它形式的low-level segmentation。

# DeepMask Proposals

DeepMask架构如下图如示：

{% asset_img deepmask.png DeepMask %}

其首先采用Vgg前部卷积层作为共享的特征提取部分，然后使用两个包含全连接的分支来预测patch包含物体在中间的得分与该patch内每个像素为前景/背景的mask。

由于mask及score均为类无关的二值概率预测，因此损失函数为两部分的binary cross entropy之和。

在预测时，将模型密集地应用于输入图片的多个位置及尺度上，这样可保证至少有一个patch能够完整地包含物体并且物体位于patch的中心。

生成proposal既要又少又准确，以average recall作为评价指标，以mask-level Intersection over Union来作为阈值标准来筛选proposal。

mask proposal中可根据闭合包含segmentation mask的bounding box作为box proposal。

# 参考文献

{% blockquote %}

Pinheiro, P. O., Collobert, R., & Dollár, P. (2015). Learning to segment object candidates. Advances in Neural Information Processing Systems, 1990-1998.

{% endblockquote %}
