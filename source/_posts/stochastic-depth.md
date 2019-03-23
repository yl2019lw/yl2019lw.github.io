---
title: Deep Networks with Stochastic Depth
date: 2019-03-23 15:39:22
tags:
  - ECCV2016
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍
较浅的神经网络表示能力不如较深的网络，但较深的网络相对难以训练，文章提出了stochastic depth方式达到了在训练时使用较浅的网络及在测试时使用较深的网络的效果，最终能大幅减少训练时间并获得性能上的提升。

测试时错误率的降低可归功于两点：一是缩短了期望的训练网络深度使得前向传播和梯度回传更加便捷，二是此种方式训练可以看作很多不同深度的网络模型进行了集成，可以有效地防止过拟合。

# Stochastic Depth

* ResNet architecture

    ResNet基本残差单元如下：
\begin{equation}
    H_l = ReLU(f_l(H_{l-1}) + id(H_{l-1}))
\end{equation}
    其中$H_l$是第l层的输出，$f_l$是由Conv, Batch Norm堆叠的层，如下图所示：

{% asset_img resnet.png resnet %}

* Stochastic depth

    文章在使用resnet block网络结构基础上，使用stochastic depth减少训练时网络的实际深度，具体而言就是通过随机地以一定概率直接丢弃整块残差单元，然后把相邻块的信息直接相连，此时有：
\begin{equation}
    H_l = ReLU(b_lf_l(H_{l_1}) + id(H_{l-1}))
\end{equation}
    其中$b_l$为伯努利分布随机变量，$p_l = Pr(b_l=1)$表示第$l$个Resnet Block被保留的概率，如果$b_l = 1$则退化到原始Resnet形式，如$b_l = 0$ 则相当于identity function， 
\begin{equation}
    H_l = id(H_{l-1})
\end{equation}

* The survival probabilities

    $p_l$为新引入的模型参数，直观地相邻层的$p_l$值应该很接近。实际上由于浅层用来提取特征并被深层使用，因此浅层被保留的概率应该更大。文章探讨了两种方式，一是设置$p_l = p_L$，对所有层有均匀的保留概率，好处是只引入一个参数；另外一种方式是线性地衰减保留概率，如初始层$p_0 = 1$，最深层为$p_L$，则中间层$l$被保留的概率为：
\begin{equation}
    p_l = 1 - \frac{l}{L}(1-p_L)
\end{equation}
    Stochastic depth网络结构如下图：

{% asset_img stochastic_depth.png stochastic depth %}

* Expected network depth

    在训练中模型的实际有效深度为$\hat{L}$，则其期望值$E(\hat{L}) = \sum_{l=1}^{L}p_l$。在$p_L = 0.5$的线性衰减策略下，$E(\hat{L}) = (3L-1)/4$，对于文章使用的110层的网络，其$L=54$，实际训练的有效深度约为40，这能显著缓解深层网络中的梯度消失与信息丢失问题。

* Training time savings

    当一个残差单元被drop时，就不需要为其进行前向及后向传播中的计算，这将大幅提升训练速度。对于上述$p_L=0.5$的线性衰减网络而言，将节省约25%的训练时间。

* Implicit model ensemble
    
    以stochastic depth方式训练时相当于将不同深度的网络模型进行集成。对于$L$个残差块的网络就有$2^L$种不同的模型组合可能，每次训练一个batch时就从这$2^L$中可能中选择一个网络，在测试时则对所有的网络进行平均。

* Stochastic depth during testing
    
    在测试时没有残差单元被丢弃，此时根据每个残差块参与训练的期望程度进行计算，测试时的前向传播如下：
\begin{equation}
    H_l^{Test} = ReLU(p_lf_l(H_{l-1}^{Test};W_l) + H_{l-1}^{Test})
\end{equation}


# 参考文献
{% blockquote %}

Huang, G., Sun, Y., Liu, Z., Sedra, D., & Weinberger, K. Q. (2016, October). Deep networks with stochastic depth. In European conference on computer vision (pp. 646-661). Springer, Cham.

{% endblockquote %}
