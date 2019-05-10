---
title: Identity Mappings in Deep Residual Networks
date: 2019-03-22 09:45:43
tags:
  - ECCV2016
  - classification
category:
  - computer vision
  - classification
mathjax: true
---

# 介绍

文章是对原始ResNet模型的改进，使得在前向与后向传播中信息能够更好地流动，进一步解决了深度学习模型训练中的退化问题。ResNet中Identity Mapping只存在shortcut connection中，改进后残差单元间传递的也是Identity Mapping。

ResNet是由一系列的残差单元堆叠而成，如下所示：
\begin{gather}
    y_l = h(x_l) + F(x_l, W_l),\\
    x_{l+1} = f(y_l)
\end{gather}
其中$x_l$是第$l$个残差单元的输入，$W_l = \{W_{l,k} | 1 \leq k \leq K\}$是第$l$个残差单元的权重，$K$是残差单元的层数(普通版$K=2$,bottleneck版本$K=3$)，$F$是残差函数，$f$是相加后的激活函数Relu，$h$是identity mapping时有$h(x_l) = x_l$。

如$f$也是identity mapping,将(2)代入(1)可得：
\begin{equation}
    x_{l+1} = x_l + F(x_l, W_l)
\end{equation}
迭代后对任意深层$L$和浅层$l$有：
\begin{equation}
    x_{L} = x_l + \sum_{i= l}^{L-1}{F(x_i, W_i) }
\end{equation}
可以看出，任意深层$L$可直接表示为任意浅层$l$加上一个残差函数，可以很方便地传递梯度：
\begin{equation}
    \frac{\partial\epsilon}{\partial{x_l}} = \frac{\partial\epsilon}{\partial{x_L}}\frac{\partial{x_L}}{\partial{x_l}} = \frac{\partial\epsilon}{\partial{x_L}}\lgroup 1 + \sum_{i=l}^{L-1}{F(x_i, W_i)}\rgroup
\end{equation}
其中$\epsilon$表示loss，公式(5)说明了对深层$L$的梯度可以直接传递到浅层$l$。

下图显示了原版ResNet及改进后的区别。
{% img /identity-mapping/identity_mapping.png 400 identity mapping %}

# Identity Skip Connections

为探讨Identity Skip Connection的重要性，先假定$f$是identity，然后考察不同形式的skip connections。
对于取$h(x_l) = \lambda_lx_l$，有
\begin{equation}
    x_{l+1} = \lambda_lx_l + F(x_l, W_l)
\end{equation}
迭代后对任意深层$L$和浅层$l$有：
\begin{equation}
    x_{L} = (\prod_{i=L}^{L-1}\lambda_i)x_l + \sum_{i= l}^{L-1}{\hat{F}(x_i, W_i)}
\end{equation}
其中$\hat{F}$为吸收了累乘因子的残差函数，进行求导有：
\begin{equation}
    \frac{\partial\epsilon}{\partial{x_l}} = \frac{\partial\epsilon}{\partial{x_L}}\lgroup (\prod_{i=L}^{L-1}\lambda_i) + \frac{\partial}{\partial{x_l}}\sum_{i=l}^{L-1}{\hat{F}(x_i, W_i)}\rgroup
\end{equation}

由于梯度中有$\prod_{i=L}^{L-1}\lambda_i$，当网络变深时容易出现梯度消失与梯度爆炸问题。
下图所示为不同的skip connections 及其结果。

{% img /identity-mapping/shortcut_connections.png 600 skip connections %}

{% img /identity-mapping/shortcut_connections_result.png 600 skip connections result %}

# Usage of Activation Functions

接下来探讨Identity在$f$方面的重要性，此时使用identity skip conntions，采用如下不同形式的残差块进行比较。

{% img /identity-mapping/diff_activations.png 600 different activations %}

如上图(c)所示可实现naive版本的identity，但由于$relu(x) = max(0, x)$总是输出非负数，会影响残差块的表达能力。
上图(d),(e)的pre-activation指将本来处于相加后使用的relu放置在残差块的最前面，且只影响残差块这一分支。此时有：
\begin{equation}
    x_{l+1} = x_l + F(\hat{f}(x_l), W_l)
\end{equation}
形式如公式(4)，信息能够得到很好的传播。

事实上由于残差网络是由很多残差单元堆叠而成，after-addition activation和pre-activation是等价的，如下图所示：

{% img /identity-mapping/pre_activation.png 600 pre activation %}

关于不同$f$调整的结果见下表：

{% img /identity-mapping/diff_activations_result.png 600 different activations result %}

# 总结

本文提出了对ResNet结构的改进，使得信息能够更好地流通。主要是将原来会阻碍信息流动的addition之后的relu位置调到了残差函数的前面，使其不影响skip connection分支。
    
# 参考文献

{% blockquote %}

He, K., Zhang, X., Ren, S., & Sun, J. (2016, October). Identity mappings in deep residual networks. In European conference on computer vision (pp. 630-645). Springer, Cham.

{% endblockquote %}
