---
title: SRGAN:Photo-Realistic Single Image Super-Resolution Using a Generative Adversial Network
tags:
  - CVPR2017
  - super resolution
category:
  - computer vision
  - super resolution
date: 2019-05-09 14:00:25
mathjax: true
---

# 介绍

文章提出了SRGAN来进行超分任务，其首次在4x放大倍数上获得了photo-realistic效果。

以MSE作为损失函数直接带来高的PSNR值，但PSNR并不能完美地代表人眼视觉体验。文章提出perceptual loss，其包含content loss及adversial loss两部分，adversial loss使用区分真实高清图片与超分生成高清图片的判别网络来迫使生成图片在视觉上逼近原始图像，content loss使用perceptual相似性来替代pixel相似性。

文章使用了mean opioion score对超分输出高清图像进行打分，SRGAN虽然没有较高的PSNR但其超分效果更好，如图所示：

{% asset_img realistic.png realistic %}

# SRGAN

## Architecture

SRGAN从$I^{LR}$超分预测$I^{SR}$，真值高清为$I^{HR}$，将输入从$W \times H \times C$映射到$rW \times rH \times C$。

SRGAN包括生成器与判别器，生成器从低分辨率输入产生预测的高清图片，判别器对真实高清图片与超分生成高分辨率图片进行区分。
\begin{equation}
    \min_{\theta_G}\max_{\theta_D} E_{I^{HR} \sim p_{train}(I^{HR})}[log D_{\theta_D}(I^{HR})] + E_{I^{LR} \sim p_{G}(I^{LR})}[log (1 - D_{\theta_D}(G_{\theta_G}(I^{HR}))] 
\end{equation}
其中$\theta_G,\theta_D$分别为生成网络与判别网络的参数。超分预测期望生成的图片接近真实高清图片：
\begin{equation}
    \hat{\theta}_G = arg \min_{\theta_G} \frac{1}{N}\sum_{n=1}^{N}l^{SR}(G_{\theta_G}(I_n^{LR}), I_n^{HR})
\end{equation}

SRGAN生成网络与判别网络如下图，相同色块代表相同的网络单元：

{% asset_img srgan.png srgan %}

其中生成器网络使用类resnet结构，使用低分辨率图片输入，在网络末端使用ESPCN中sub-pixel conv学习上采样参数输出高分辨率预测。
其中判别器网络使用类vgg结构，网络末端使用sigmoid进行二分类，即判断其输入高分辨率图片是来自真实数据集还是通过超分解析获得。

## Perceptual loss function

perceptual loss $l^{SR}$包含content loss及adversarial loss两部分。
\begin{equation}
    l^{SR} = \underbrace{l_X^{SR}}_{content \quad loss} + \underbrace{10^{-3}l_{Gen}^{SR}}_{adversarial \quad loss}
\end{equation}

### Content loss

Content loss衡量生成图片与真实图片的相似性，原MSE loss为:
\begin{equation}
    l_{MSE}^{SR} = \frac{1}{r^2HW}\sum_{x=1}^{rW}\sum_{y=1}^{rH}(I_{x,y}^{HR} - G_{\theta_G}(I^{LR})_{x,y})^2
\end{equation}

SRGAN提出content loss度量使用vgg中间层feature map间的差异：
\begin{equation}
    l_{VGG/i,j}^{SR} = \frac{1}{W_{i,j}H_{i,j}}\sum_{x=1}^{W_{i,j}}\sum_{y=1}^{H_{i,j}}(\phi_{i,j}(I^{HR})_{x,y} -\phi_{i,j}(G_{\theta_G}(I^{LR}))_{x,y})^2
\end{equation}
其中$\phi_{i,j}$表示pool i后conv j处的feature map.

SRResNet为仅使用SRGAN生成部分网络进行超分解析且使用MSE作为损失函数。

### Adversarial loss

SRGAN使用adversarial loss鼓励网络生成判别网络难以区分的近真实图像。
\begin{equation}
    l_{Gen}^{SR} = \sum_{n=1}^{N}-log D_{\theta_D}(G_{\theta_G}(I^{LR}))
\end{equation}

# 参考文献

{% blockquote %}

Ledig, C., Theis, L., Huszár, F., Caballero, J., Cunningham, A., Acosta, A., ... & Shi, W. (2017). Photo-realistic single image super-resolution using a generative adversarial network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4681-4690).

{% endblockquote %}
