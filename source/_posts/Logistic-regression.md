---
title: Logistic-regression
date: 2019-06-03 09:33:30
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### Logistic回归算法

&emsp;&emsp;Logistic分布的分布函数和密度函数分别是:
$$
    \begin{aligned}
        F(x)&=\frac{1}{1+e^{-(x-\mu)/\gamma}} \\
        f(x)&=\frac{e^{-(x-\mu)/\gamma}}{\gamma(1+e^{-(x-\mu)/\gamma})^2}
    \end{aligned}
$$
&emsp;&emsp;其中$\mu$是位置参数,$\gamma>0$是形状参数.
分布函数曲线以点$(\mu,\frac{1}{2})$为中心对称.

&emsp;&emsp;对于二项Logistic回归模型,条件概率分布为:
$$
    \begin{aligned}
        P(Y=1|x)&=\frac{exp(\omega\cdot x)}{1+exp(\omega\cdot x)} \\
        P(Y=0|x)&=\frac{1}{1+exp(\omega\cdot x)}
    \end{aligned}
$$
&emsp;&emsp;其中$\omega$是参数,可以用极大似然估计法估计参数,似然估计函数为:
$$\prod^N_{i=1}[P(Y=1|x_i)]^{y_i}[1-P(Y=1|x_i)]^{1-y_i}$$
&emsp;&emsp;对数似然函数就是:
$$
    \begin{aligned}
        L(\omega)&=\sum^N_{i=1}[y_ilogP(Y=1|x_i)+(1-y_i)log(1-P(Y=1|x_i))] \\
        &=\sum^N_{i=1}[y_i(\omega\cdot x_i)-log(1+exp(\omega\cdot x_i))]
    \end{aligned}
$$
&emsp;&emsp;问题就等价于以对数似然函数为目标函数的最优化问题,可以用梯度下降法或牛顿法.

---
&emsp;&emsp;Logistic算法实现简单,速度快,但是当特征空间很大时,性能不是很好；容易欠拟合,一般准确率不太高；对于非线性特征需要转换