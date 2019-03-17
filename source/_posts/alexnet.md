---
title: AlexNet:ImageNet Classification with Deep Convolutional Neural Networks
tags:
  - NIPS2012
  - classification
category:
  - computer vision
  - classification
mathjax: true
date: 2019-03-12 10:14:46
---

# 介绍
AlexNet根据其作者Alex命名,Alex是深度学习之父Hinton的学生。AlexNet赢得了ILSVRC-2012比赛的冠军，点燃了深度学习之火。


# 数据集
ImageNet是由斯坦福李飞飞主导发布大规模图像数据集, 包含超过1500万张有标注信息的高清图片，从属于约22000个类别。ILSVRC使用ImageNet的一个子集，共有1000个目标类别，其中约有1200万张用作训练集，5万张用于验证集，15万张用于测试集。

分类任务使用top-1 error和top-5 error两个评价指标。top-1 error指在测试集上预测错误的图片所占的比率，top-5 error指的是在测试集上预测概率最高的五个类别均不包含正确类别的比率。

ImageNet包含大小不同的图片，模型需要固定大小的输入。文章首先把原始图片缩放至短边大小为256，再从中截取256x256的patch。


# 架构
AlexNet包含8个可学习参数的层，其中5个卷积层，3个全连接层。摘自torchvision仓库的实现如下：

{% codeblock %}
class AlexNet(nn.Module):

    def __init__(self, num_classes=1000):
        super(AlexNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 6 * 6, 4096),
            nn.ReLU(inplace=True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        x = self.features(x)
        x = self.avgpool(x)
        x = x.view(x.size(0), 256 * 6 * 6)
        x = self.classifier(x)
        return x
{% endcodeblock %}

文章认为对分类结果有帮助的几个点：

* 使用Relu 

        f(x) = max(0, x)

* 多GPU训练

* 局部响应正则化  

        后被其它文章证实无用，并且会带来额外计算负担。

* 重叠池化

# 实验

* 数据增广
    - 水平镜像及在256x256图片上密集取224x224 patch。
    - 随机改变RGB通道上亮度值。

* Dropout

* 结果

        在ILSVRC—2010上获得top-1 error为37.5%，top-5 error为17.0%。ILSVRC-2012上top-5 error为15.3%，第二名为26.2%。


# 参考文献
{% blockquote %}

Krizhevsky, A., Sutskever, I., & Hinton, G. E. (2012). Imagenet classification with deep convolutional neural networks. Advances in neural information processing systems, 1097-1105.

{% endblockquote %}
