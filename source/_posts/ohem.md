---
title: OHEM:Training Region-based Object Detectors with Online Hard Example Mining
tags:
  - CVPR2016
  - detection
category:
  - computer vision
  - detection
  - two-stage detector
date: 2019-04-20 14:43:07
---

# 介绍

在目标检测任务中，针对训练数据集中存大的大量容易的样本和少量的困难样本，文章提出了Online hard example mining，自动选择对模型而言相对困难的样本参与训练。

在学习任务中，可能出现包含大量容易的负样本，此时采用hard negative mining来促进训练，此策略交替式地进行模型训练与困难样本选择。即在固定模型后，根据样本的loss选择相对困难的样本加入训练集，然后再固定挑选出来的样本组成的训练集来更新模型，交替式地不断重复此过程。R-CNN和Spp-net使用CNN提取特征后进行存储，再使用SVM对候选框分类，可以自然地使用hard negative mining策略。然而在如Fast
R-CNN端到端训练系统中固定模型来选择样本会拖慢速度。

# OHEM

在Fast R-CNN中，RoI与groud-truth box IoU高于0.5为正样本，否则为负样本，在每个batch内对负样本欠采样使正负样本比为1:3。Fast R-CNN采用hierachical采样，既先选图片，再从图片中选取一定数量的RoI，这样同一图片的特征提取部分计算是共享的。

OHEM的思路在于在线选择困难样本，同样是先选择图片，但从每个图片中选择最困难的那些RoI参与后续的训练。RoI的困难程度是根据后续bbox head的loss来确定的，所以不同于之前对每张图片仅计算固定数量的RoI的loss及反向传播，OHEM需要对所选取的图片的全部RoI计算其loss。虽然如此，因为特征提取是针对图片级别共享的，仅需对所有的RoI执行RoI pooling等部分的计算，因此额外的开销可负担。

同时，同一张图片中有些RoI会有较大的重叠，如果均使用这些RoI会引入loss的重复计入，因此采用non-maximum suppression来过滤RoI，即给定一张图片的一系列RoI及其对应loss，选择那些有较高loss的RoI，并去除那些与更高loss的RoI有IoU超过0.7的RoI。

在实现OHEM时有两种策略，一是对图片的RoI全部计算loss，但仅针对选中的hard example进行梯度反传，未选中的RoI将其loss置为0不参与后续训练;二是维持二份拷贝的RoI network，其中只读的Roi network对全部Roi计算loss用于选择困难样本，另一Roi network使用选择出来的困难样本进行正常forward与backward更新。

{% asset_img ohem.png online hard exmaple mining %}

# 参考文献

{% blockquote %}

Shrivastava, A., Gupta, A., & Girshick, R. (2016). Training region-based object detectors with online hard example mining. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 761-769).

{% endblockquote %}
