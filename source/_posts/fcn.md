---
title: FCN:Fully convolutional networks for semantic segmentation
tags:
  - CVPR2015
  - segmentation
category:
  - computer vision
  - segmentation
  - semantic segmentation
date: 2019-04-23 21:45:38
mathjax: true
---

# 介绍

文章提出了通过全卷积网络进行图像语义分割，同时使用了skip architecture结合来自网络深层有更强语文信息的特征与来自浅层有更细粒度位置的特征，获得了state-of-the-art性能。语义分割为图片中每一个像素位置输出其类别，网络示意图如下：

{% img /fcn/semantic.png 400 semantic %}

# Segmentation Architecture

FCN将通常卷积网络如VGG中全连接层替换为卷积层，整个网络为全卷积网络后输出大小与输入大小对应成比例，网络经过strided conv与pooling层时feature map size减小，因输出类别score map与输入图片大小一致，需要在网络中进行上采样操作，这是通过Deconvolution来完成的，Deconvolution参数可学习，由双线性插值权重进行初始化。

{% codeblock %}
def upsample_filt(size):
    """
    Make a 2D bilinear kernel suitable for upsampling of the given (h, w) size.
    """
    factor = (size + 1) // 2
    if size % 2 == 1:
        center = factor - 1
    else:
        center = factor - 0.5
    og = np.ogrid[:size, :size]
    return (1 - abs(og[0] - center) / factor) * \
           (1 - abs(og[1] - center) / factor)
{% endcodeblock %}

文章采用VGG作为backbone，网络结构如下：
{% asset_img fcn.png fcn %}
其中全连接层替换为卷积conv6-7，使用最终feature map直接上采样32倍得到score map为FCN32s，结合了pool3及pool4信息输出score map的模型分别为FCN-8s与FCN-16s，注意并未结合pool5。FCN-16s通过将conv7输出上采样2倍与pool4输出相加后上采样16倍获得，FCN-8s通过将conv7输出上采样4倍，pool4输出上采样2倍与pool3输出相加后再上采样8倍获得。训练时可使用FCN32s的参数对FCN-16s进行初始化，使用FCN-16s参数对FCN-8s参数进行初始化。

# Metric

令$n_{ij}$为把属于类i的像素预测为类j的像素数目，共有$n_{cl}$个不同的类，$t_i = \sum_j n_{ij}$ 为属于类i的像素数目，则可计算以下指标：

pixel accuracy: $\sum_i n_{ii} / \sum_i t_i$

mean accuracy: $(1/n_{cl}) \sum_i n_{ii} / t_i$

mean IU: $(1/n_{cl}) \sum_i n_{ii} / (t_i + \sum_j n_{ji} - n_{ii})$

frequency weighted IU: $(\sum_k t_k)^{-1} \sum_i t_in_{ii} / (t_i + \sum_j n_{ji} - n_{ii})$

# 参考文献

{% blockquote %}

Long, J., Shelhamer, E., & Darrell, T. (2015). Fully convolutional networks for semantic segmentation. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 3431-3440).

https://github.com/shelhamer/fcn.berkeleyvision.org

{% endblockquote %}
