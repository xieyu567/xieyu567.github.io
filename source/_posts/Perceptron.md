---
title: Perceptron
date: 2019-05-29 10:22:37
categories: 统计学习方法
tags: machine learning
mathjax: true
---
#### 感知机算法
  
感知机算法是一种二分类的线性分类模型,仅适用线性可分的二分类问题.

---
##### 算法步骤:
1. 选取初值$\omega_0,b_0$
2. 在训练集中选取数据$(x_i,y_i)$
3. 如果$y_i(\omega\cdot x_i+b)\leq0$
$$
\begin{aligned}
    \omega&\leftarrow\omega +\eta y_ix_i \\
    b&\leftarrow b+\eta y_i
\end{aligned}
$$
4. 重复算法,直至没有误分类点.

##### 感知机算法的对偶形式:
1. $\alpha\leftarrow0,b\leftarrow0$
2. 在训练集中选取数据$(x_i,y_i)$
3. 如果$y_i(\sum^N_{j=1}\alpha_jy_jx_j\cdot x_i+b)\leq0$

$$
    \begin{aligned}
        \alpha_i&\leftarrow \alpha_i +\eta \\
        b&\leftarrow b+\eta y_i
    \end{aligned}
$$
4. 重复算法,直至没有误分类点.

对偶形式中用到了Gram矩阵,也就是对样本集做内积:
$$G=[x_i\cdot x_j]_{N\times N}$$

---
感知机算法的缺点让这个算法的应用范围很窄,基本不会被实际用到.但是通过不同方向的改进,能够得到一些新的算法:比如因为感知机算法得到的解,依赖于初值的选择和迭代过程中选择误分类点的顺序,为了得到唯一超平面,需要引入约束条件,这就是线性SVM的思想；针对线性不可分的问题,通过多层感知机算法解决.