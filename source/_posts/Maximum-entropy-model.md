---
title: Maximum-entropy-model
date: 2019-06-03 09:53:09
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### 最大熵模型

&emsp;&emsp;最大熵模型是生成模型,根据最大熵原理,在满足约束条件的模型集合中选择熵最大的模型.在模型集合C中,最大熵模型是条件概率分布$P(Y|X)$上的条件熵$H(P)$最大的模型:
$$H(P)=-\sum_{x,y}\widetilde{P}(x)P(y|x)logP(y|x)$$
其中$\widetilde{P}{x}$是经验分布.
___
##### 最大熵模型推导
&emsp;&emsp;条件熵$P(Y|X)$表示已知$X$的情况下$Y$的不确定性,定义为给定$X$的条件下$Y$的条件概率分布的熵对$X$的期望.
$$
\begin{aligned}
H(Y|X)&=\sum_x p(x)H(Y|X=x) \\
&=-\sum_x p(x)\sum_y p(y|x)logp(y|x) \\
&=-\sum_{x,y}p(x,y)logp(y|x)  
\end{aligned}
$$
&emsp;&emsp;如果模型能够获取训练数据中的信息,那么可以假设两个期望相等:
$$
\begin{aligned}
E_P(X,Y)&=E_{\widetilde{P}}(X,Y) \\
-\sum_{x,y}\widetilde{P}(x)P(y|x)logp(y|x)&=-\sum_{x,y}\widetilde{P}(x,y)logp(y|x) 
\end{aligned}
$$
&emsp;&emsp;由此可以得到最大熵模型的公式.

---
##### 最大熵模型学习过程
&emsp;&emsp;最大熵模型的学习问题:
$$
\begin{aligned}
max_{P\in C}&H(p)=-\sum_{x,y}\widetilde{P}(x)P(y|x)logP(y|x)\\
 s.t.\qquad &E_P(f_i)=E_{\widetilde{P}(f_i)}, \quad i=1,2,...,n \\
 &\sum_yP(y|x)=1
\end{aligned}
$$
&emsp;&emsp;引入拉格朗日乘子$\omega$,得到拉格朗日函数$L(P,\omega)$:
$$
\begin{aligned}
    L(P,\omega)&\equiv-H(P)+\omega_0(1-\sum_y P(y|x))+\sum^n_{i=1}\omega_i(E_{\widetilde{P}}(f_i)-E_P(f_i)) \\
    &=\sum_{x,y}\widetilde{P}(x)P(y|x)logP(y|x)+\omega_0(1-\sum_y P(y|x))+ \\
    &\quad\sum^n_{i=1}\omega_i(\sum_{x,y}\widetilde{P}(x,y)f_i(x,y)-\sum_{x,y}\widetilde{P}(x)P(y|x)f_i(x,y))
\end{aligned}
$$
&emsp;&emsp;其中$f_i(x,y)$是二值函数,$x,y$同时满足条件时为1,其他情况为0

&emsp;&emsp;拉格朗日函数的优化问题是:
$$min_{P\in C}max_\omega L(P,\omega)$$
&emsp;&emsp;根据拉格朗日对偶性,可以求其对偶问题:
$$max_\omega min_{P\in C}L(P,\omega)$$
&emsp;&emsp;首先求$L(P,\omega)$关于$P$的极小值问题.即求$L(P,\omega)$对$P(y|x)$的偏导数.
$$
\begin{aligned}
    \frac{\partial L(P,\omega)}{\partial P(y|x)}&=\sum_{x,y}\widetilde{P}(x)(logP(y|x)+1)-\sum_y\omega_0-\sum_{x,y}(\widetilde{P}(x)\sum^n_{i=1}\omega_i f_i(x,y)) \\
    &=\sum_{x,y}\widetilde{P}(x)(logP(y|x)+1-\omega_0-\sum^n_{i=1}\omega_i f_i(x,y))
\end{aligned}
$$
&emsp;&emsp;令上式为0,$\widetilde{P}(x)$>0时,
$$P(y|x)=exp(\sum^n_{i=1}\omega_i f_i(x,y)+\omega_0-1)=\frac{exp(\sum^n_{i=1}\omega_i f_i(x,y))}{exp(1-\omega_0)}$$
&emsp;&emsp;因为约束条件$\sum_y P(y|x)=1$:
$$\sum_yP(y|x)=\sum_y \frac{exp(\sum^n_{i=1}\omega_i f_i(x,y))}{exp(1-\omega_0)}=1$$
$$exp(\omega_0-1)=\frac{1}{\sum_yexp(\sum^n_{i=1}\omega_i f_i(x,y))}$$
&emsp;&emsp;令$Z_\lambda(x)=\sum_yexp(\sum^n_{i=1}\omega_i f_i(x,y))$,$Z_\omega(x)$被称为规范化因子,最终的式子为:
$$P_\omega(y|x)=\frac{1}{Z_\omega(x)}exp(\sum^n_{i=1}\omega_i f_i(x,y))$$
&emsp;&emsp;然后再求极大值问题,也就是再对$\omega$求偏导,令式子为0,求得$\omega$的极大值:
$$argmax_\omega\Psi(\omega)$$
&emsp;&emsp;最后这一步求极大值等价于最大熵模型的极大似然函数,似然函数的形式为条件概率分布为$P(y|x)$的对数似然函数.