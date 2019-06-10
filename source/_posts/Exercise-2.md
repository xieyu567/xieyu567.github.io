---
title: Exercise-2
date: 2019-06-03 20:40:17
updated: 
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### 章节2的习题解
##### 习题1
&emsp;&emsp;因为异或本身不是线性可分的,不光是感知机模型不能表示异或,只要是非线性模型都无法表示异或.

![](https://ws3.sinaimg.cn/large/005BYqpggy1g3oahx3no8j30hs0dct90.jpg)

---
##### 习题3
&emsp;&emsp;先证明必要性.即由线性可分$\Rightarrow$凸壳互不相交.

&emsp;&emsp;样本集线性可分,即存在超平面$wx+b=0$分割样本集,对于每个正实例点都有$wx_i+b>0$, 每个负实例点都有$wx_i+b<0$

&emsp;&emsp;利用反证法,假如正负实例点集是相交的,那么一定存在点既属于正实例点集又属于负实例点集.与上述相矛盾.因此必要性得证.

&emsp;&emsp;然后证明充分性,即由凸壳互不相交$\Rightarrow$线性可分.

&emsp;&emsp;对于正实例点集和负实例点集必然存在距离最近的不同类的两点距离$||x_1,x_2||_2>0$,其他各点之间的距离都大于$||x_1,x_2||_2$

&emsp;&emsp;那么设一个超平面$wx+b=0$,其中:
$$w=x_1-x_2$$
$$b=\frac{x_1\cdot x_1-x_2\cdot x_2}{2}$$
&emsp;&emsp;因此
$$wx+b=\frac{||x_2-x||_2^2-||x_1-x||^2_2}{2}$$
对于和$x_2$同一类的点,可得到$||x_2-x||_2^2\leq ||x_1-x||^2_2$,因此$wx+b<0$,对于和$x_1$同一类的点,则可以得到相反的结果$wx+b>0$.因此得证线性可分.