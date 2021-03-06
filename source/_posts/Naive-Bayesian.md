---
title: Naive Bayesian
date: 2019-05-27 16:52:44
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### 朴素贝叶斯算法

朴素贝叶斯算法是典型的生成模型,生成模型由训练数据学习联合概率分布$P(X,Y)$,然后求得后验概率分布$P(Y|X)$.
$$P(X,Y)=P(Y)P(X|Y)$$

---
##### 算法步骤:
1. 计算先验概率及条件概率
$$P(Y=c_k)=\frac{\sum^N_{i=1}I(y_i=c_k)}{N}$$
$$P(X^(i)=a_{jl}|\frac{\sum^N_{i=1}I(x^{(j)}_i=a_{jl},y_i=c_k)}{\sum^N_{i=1}I(y_i=c_k)})$$
其中概率计算方法可以是极大似然估计或贝叶斯估计.

2. 对于给定的实例x计算
$$P(Y=c_k)\prod^n_{j=1}P(X^{(j)}=x^{(j)}|Y=c_k)$$
3. 确定实例$x$的类
$$y=argmax_{c_k}P(Y=c_k)\prod^n_{j=1}P(X^{(j)}=x^{(j)|Y=c_k})$$

&emsp;&emsp;对于连续值,采用的是高斯模型,即假设特征符合高斯分布；对于离散值,采用多项式模型；另外在伯努利模型中,每个特征的取值是布尔型,这个模型我还不是很清楚在什么场景下使用.

---
&emsp;&emsp;朴素贝叶斯的基本假设是条件独立性,这是一个严格的条件,因此模型所需要的参数比较少.这个算法的优点主要有:
1. 对小规模的数据表现较好,能处理多分类任务.
2. 面对无关属性,$P(X_i|Y)$几乎是均匀分布,$X_i$类的条件概率不会对总的后验概率计算产生影响.

&emsp;&emsp;缺点主要有:
1. 需要计算先验概率.
2. 输入变量必须是条件独立的,如果样本点之间存在概率依存关系,那么相关属性可能会降低朴素贝叶斯分类器的性能.