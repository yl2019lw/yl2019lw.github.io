---
title: R-CNN:Rich feature hierarchies for accurate object detection and semantic segmentation
tags:
  - CVPR2014
  - detection
category:
  - computer vision
  - detection
date: 2019-04-09 17:21:26
---

# 介绍

文章提出了R-CNN模型，即Regions with CNN features，使得在VOC上目标检测的mAP指标提高至53.3%，大幅优于之前的系统。

PASCAL VOC数据集主要有voc2007与voc2012两个不同的版本，voc2012中包含了2008-2011年新增加的图片。VOC中包含了20个类别的图片并给出了bounding box标注信息，官方提供数据划分比例约为训练：测试为1：1。

对于目标检测任务，常用的评价指标是mean average precision。mean是对所有的类别的average precision求平均，average precision是每个类在控制不同recall水平时平均意义的precision。

# R-CNN

R-CNN首先由输入图片产生一些与类别无关的region proposal，作为可能的目标检测框，然后使用卷积神经网络从这些候选框中提取固定大小的特征，再对提取出的特征使用线性svm分别针对每个类别计算得分，认为所框选的部分为得分高的对应物体类别。最终也会用bbox regression来对region proposal进行调整，使得检测的位置更精确。整体流程如下：

{% img /rcnn/rcnn.png 600 rcnn %}

## Region proposal

R-CNN使用selective search来产生类别无关的候选框。

## Feature extraction

文章使用caffe实现的alexnet提取特征，使用预训练ImageNet权重进行初始化，输入都warp到227x227大小并减去像素级别均值,最终得到每个proposal的4096维的特征表示。在训练时与ground truth的IoU(Intersection over Union)超过50%的proposal为正类，否则为负类。在每个batch内采样128个proposal，其中32个为正样本，96个为负样本。

## Test-time detection

测试时先使用selective search对输入图片产生约2000个region proposal,然后把warp后的proposal输入训练好的CNN得到特征表示，然后将特征输入到每个类对应的支持向量机得到该类的得分。对于一张图片就有了一系列带类别分值的bounding box，此时对这些bouding box进行non-maximum suppression来去除那些重复的检测框。具体而言，首先对每个类别对应的bounding box按此类的得分进行排序，得分最高的box被选出为可用,然后把与此box的IoU超过一定阈值的box丢弃，再在剩下的box中重复进行此过程直至筛选出全部高得分并且IoU较小的bounding box。

# 参考文献

{% blockquote %}

Girshick, R., Donahue, J., Darrell, T., & Malik, J. (2014). Rich feature hierarchies for accurate object detection and semantic segmentation. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 580-587).

{% endblockquote %}
