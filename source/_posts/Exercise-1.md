---
title: Exercise 1
date: 2019-05-30 11:20:30
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### 章节1的习题解
##### 习题1
&emsp;&emsp;似然估计用于在已知一些参数的情况下,预测接下来在观测上所得到的结果.因为似然函数只与采样数据有关,因此也被称为采样分布.

&emsp;&emsp;对于一个概率分布$D$,已知概率密度函数为$f_D$,一个参数$\theta$,抽出有n个值的采样,则似然函数为
$$L(x_1,...,x_n|\theta)=f_\theta(x_1,...,x_n)$$
&emsp;&emsp;因此求极大似然估计就是将要估计的参数代入概率密度函数后求导.

&emsp;&emsp;对于伯努利模型,将结果为1的概率$\theta$代入概率密度函数:
$$L(\theta)=\prod^n_{i=1}P(x_i)=\theta^k(1-\theta)^{n-k}$$
&emsp;&emsp;对公式求导:
$$
\begin{aligned}
    \frac{\partial L(\theta)}{\partial \theta}&=k\theta^{k-1}(1-\theta)^{n-k-1}-n\theta^k(1-\theta)^{n-k-1} \\
    &=(k-n\theta)\theta^{k-1}(1-\theta)^{n-k-1}
\end{aligned}
$$
&emsp;&emsp;令上式为0,得到$\theta=\frac{k}{n}$

---
&emsp;&emsp;贝叶斯估计的公式为:
$$P(\theta|x)=\frac{P(x|\theta)P(\theta)}{P(x)}$$
&emsp;&emsp;因为$P(x)$与$\theta$无关:
$$P(\theta|x)\propto P(x|\theta)P(\theta)$$
&emsp;&emsp;可以看到贝叶斯估计相当于似然函数乘上一个先验分布.$t$和$r$是先验信息.将参数代入到上式:
$$
    \begin{aligned}
        argmax_\theta P(\theta|x)&=\theta^k(1-\theta)^{n-k}\theta^t(1-\theta)^r \\
        &=\theta^{k+t}(1-\theta)^{n-k+r} \\
    \end{aligned}
$$
$$
    \begin{aligned}
        \frac{\partial P(\theta|x)}{\partial\theta}&=(k+t)\theta^{k+t-1}(1-\theta)^{n-k+r}-(n-k+r)\theta^{k+t}(1-\theta)^{n+r-k-1} \\
        &=[k+t-(t+n+r)\theta]\theta^{k+t=1}(1-\theta)^{n-k+r-1}
    \end{aligned}
$$
&emsp;&emsp;令上式为0,得到$\theta=\frac{k+t}{n+t+r}$

---
##### 习题2
&emsp;&emsp;经验风险最小化:
$$min_{f\in F}=\frac{1}{N}\sum^N_{i=1}L(y_i,f(x_i))$$
&emsp;&emsp;根据条件有,损失函数为对数损失函数,即:
$$L(y_i,f(x_i))=-logP(y_i|x_i)$$
&emsp;&emsp;将损失函数代入,得到:
$$min_{P}\frac{1}{N}\sum^N_{i=1}(-logP(y_i|x_i))=\frac{1}{N}max_plog(\prod^N_{i=1}P(y_i|x_i))$$
&emsp;&emsp;去掉不相关项得到$max_p\prod^N_{i=1}P(y_i|x_i)$. 
显然这是极大似然估计,因此在模型是条件概率分布,损失函数是对数损失函数时,经验风险最小化等价于极大似然估计.