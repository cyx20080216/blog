---
author: "cyx20080216"
title: "全连接神经网络浅析"
date: 2022-04-15T22:41:00+08:00
lastmod: 2022-04-16T12:12:00+08:00
tags: ["算法", "机器学习"]
categories: ["机器学习"]
---
![全连接神经网络简图](/static/images/neural-network-structure.png)

这是一个全连接神经网络的简图，由一个输入层，两个隐藏层和一个输出层构成

在这篇文章中，我将详细的讲讲它和其它类似的神经网络怎么工作，以及反向传播的完整推导过程
# 神经元
将一个神经元放大来看，大概是这样：

![神经元简图](/static/images/neural-structure.png)

其中， $w_i$ 是权重， $b$ 是偏置， $f(x)$ 是激活函数

激活函数有很多种，常用的有 Sigmoid, ReLU 等，但它们的目的都是为了增强网络的拟合能力，进行**非线性的拟合**

**激活函数必须是非线性的**

令 $n$ 为输入数量， $x_i$ 为每个输入的值

在激活之前，它的输出为输入加权求和后的值。即 $q=\sum_{i=1}^nw_ix_i+b$

激活之后，它的输出为： $r=f_k(q)=f(\sum_{i=1}^nw_ix_i+b)$

请务必记牢这两个式子，后面会频繁地使用它们
# 约定
网络层数（不包括输入层）： $T$

网络输入数量： $n_0$

第 $k$ 层神经元数量： $n_k$

第 $k$ 层的激活函数： $f_k(x)$

连接第 $k$ 层第 $i$ 个神经元与上一层第 $j$ 个神经元的权重： $w_{kij}$

第 $k$ 层第 $i$ 个神经元的偏置： $b_{ki}$

网络的第 $i$ 个输入： $r_{0i}$

第 $k$ 层第 $i$ 个神经元激活前的输出： $q_{ki}$

第 $k$ 层第 $i$ 个神经元激活后的输出： $r_{ki}$
# 前向传播
之前，我们已经知道了每一个神经元的工作方法

那么这一步十分简单，用一样的方法即可

$$q_{ki}=\sum_{j=1}^{n_{k-1}}w_{kij}r_{{k-1}j}+b_{ki}$$

$$r_{ki}=f_k(q_{ki})$$

最后一层神经元激活后的输出，就是整个神经网络的输出
# 训练
到目前为止，我们还有一个很大的问题：参数从哪来？

类似于网络层数、神经元数量这些关于网络结构以及之后要说的训练过程的参数，我们称为**超参数**

这些参数通常由网络设计者来设定

而权重和偏置，手动设定显然不现实。所以，我们通过让网络自己学习这些参数，这就是**训练**

为了训练神经网络，首先要设计一个损失函数，用来评估模型的准确度

如 $L(x_1, x_2, ... x_n, y_1, y_2, ... y_n)=\sum_{i=1}^n(x_i-y_i)^2$ 就是一种常用的损失函数

我们令期望输出的第 $i$ 个值为 $y_i$

我们令损失值 $l=L(r_{T1}, r_{T2}, ... r_{Tn_T}, y_1, y_2, ... y_{n_T})$

想要找到最合适的参数，就是找到 $l$ 的低谷

我们可以通过一些方法求出 $\dfrac{dl}{dw_{kij}}$ 和 $\dfrac{dl}{dw_{bi}}$

求出了微分，我们就能够知道如何调整参数才能让 $l$ 尽可能快地减小

这就是**梯度下降法**

我们使用**优化器**这一概念来描述调整的方法，我们令其为 $O(w, d, \eta)$

最简单的一种就是 $O(w, d, \eta)=w-\eta d$

其中的 $\eta$ 指学习率，这也是个超参数

通常情况下，太高的学习率会导致损失值不稳定，太低会导致学习过慢

一般，我们可以取 $\eta=0.01$

这样一来，只需不断使

$$w_{kij}\gets O(w_{kij}, \dfrac{dl}{dw_{kij}}, \eta)$$

$$b_{ki}\gets O(b_{ki}, \dfrac{dl}{db_{ki}}, \eta)$$

就可以得到较优的参数
# 反向传播
现在，我们来说说如何求出 $\dfrac{dl}{dw_{kij}}$ 和 $\dfrac{dl}{db_{ki}}$ ，这也是最难的一部分

**高能预警，请有一定的微分基础后再往下读**

首先，根据 $q_{ki}=\sum_{j=1}^{n_{k-1}}w_{kij}r_{{k-1}j}+b_{ki}$ ，我们得出

$$
\begin{aligned}
&\dfrac{dl}{dw_{kij}}=\dfrac{dl}{dq_{ki}}\cdot\dfrac{dq_{ki}}{dw_{kij}}\\
&\;\;\;\;\;\;\;\;\;=\dfrac{dl}{dq_{ki}}\cdot r_{{k-1}j}\\
\end{aligned}
$$

$$
\begin{aligned}
&\dfrac{dl}{db_{ki}}=\dfrac{dl}{dq_{ki}}\cdot\dfrac{dq_{ki}}{db_{ki}}\\
&\;\;\;\;\;\;\;=\dfrac{dl}{dq_{ki}}\\
\end{aligned}
$$

$$
\begin{aligned}
&\dfrac{dl}{dq_{ki}}=\sum_{j=1}^{n_{k+1}}\dfrac{dl}{dq_{{k+1}j}}\cdot\dfrac{dq_{{k+1}j}}{dr_{ki}}\cdot\dfrac{dr_{ki}}{dq_{ki}}\\
&\;\;\;\;\;\;\;=\sum_{j=1}^{n_{k+1}}\dfrac{dl}{dq_{{k+1}j}}\cdot w_{{k+1}ji}\cdot f_k^{'}(q_{ki})\\
&\;\;\;\;\;\;\;=(\sum_{j=1}^{n_{k+1}}\dfrac{dl}{dq_{{k+1}j}}\cdot w_{{k+1}ji})\cdot f_k^{'}(q_{ki})\\
\end{aligned}
$$

根据 $l=L(r_{T1}, r_{T2}, ... r_{Tn_T}, y_1, y_2, ... y_{n_T})$ ，可以得出

$$
\begin{aligned}
&\dfrac{dl}{dq_{Ti}}=\dfrac{dl}{dr_{Ti}}\cdot\dfrac{dr_{Ti}}{dq_{Ti}}\\
&\;\;\;\;\;\;\;=\dfrac{dl}{dr_{Ti}}\cdot f_T^{'}(q_{Ti})\\
\end{aligned}
$$

得出了这几条公式，我们就能从后往前算出所有的 $\dfrac{dl}{dq_{ki}}$ ，再用它们求出所有的 $\dfrac{dl}{dw_{kij}}$ 和 $\dfrac{dl}{db_{ki}}$ ，用来进行梯度下降
# 总结
总结起来，重要的公式无外乎这几个：

$$q_{ki}=\sum_{j=1}^{n_{k-1}}w_{kij}r_{{k-1}j}+b_{ki}$$

$$r_{ki}=f_k(q_{ki})$$

$$l=L(r_{T1}, r_{T2}, ... r_{Tn_T}, y_1, y_2, ... y_{n_T})$$

$$\dfrac{dl}{dq_{Ti}}=\dfrac{dl}{dr_{Ti}}\cdot f_T^{'}(q_{Ti})$$

$$\dfrac{dl}{dq_{ki}}=(\sum_{j=1}^{n_{k+1}}\dfrac{dl}{dq_{{k+1}j}}\cdot w_{{k+1}ji})\cdot f_k^{'}(q_{ki})$$

$$\dfrac{dl}{dw_{kij}}=\dfrac{dl}{dq_{ki}}\cdot r_{{k-1}j}$$

$$\dfrac{dl}{db_{ki}}=\dfrac{dl}{dq_{ki}}$$

$$w_{kij}\gets O(w_{kij}, \dfrac{dl}{dw_{kij}}, \eta)$$

$$b_{ki}\gets O(b_{ki}, \dfrac{dl}{db_{ki}}, \eta)$$
# 再谈训练
在训练一个模型时，需要将数据集分成两部分：训练集和测试集

测试集不能以任何形式参与训练，仅用于测试模型准确性

若模型在训练集上表现优异，但在测试集上表现不佳，可能是出现了**过拟合**，这时可以通过减少网络层数和增加训练集的数据量来解决