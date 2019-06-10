---
title: Deep Learning
date: 2019-06-05 10:00:57
updated: 
categories: papers
tags:
visitors: 
mathjax: true
---
&emsp;&emsp;这篇论文是2015年深度学习三巨头:Yann LeCun(Facebook AI研究院,纽约大学), Yoshua Bengio(蒙特利尔大学), Geoffrey Hinton(Google,多伦多大学)在Nature发表的综述文章.

&emsp;&emsp;文中先阐述了深度学习是一种拥有多层级的特征学习方法.通过非线性单元将单层特征转换成多层级特征,这样能够得到高维,抽象程度高的复杂函数来表示特征.深度学习擅长的领域在于发掘多维数据中的复杂结构.

&emsp;&emsp;之后的两章指出为什么,怎样. 因为线性分类器提取特征效果不佳$\Rightarrow$使用深度学习来提取特征. 深度学习大多使用反向传播神经网络架构. 

&emsp;&emsp;接下来文中介绍了深度卷积网络在CV中的应用. 在NLP领域,文中指出了深度学习理论比起传统方法的两个优势. 一是学习到的分布式表示能够生成新的组合, 二是隐层带来的一些潜在的特征. 

&emsp;&emsp;这篇文章主要还是在监督学习领域讨论深度学习的发展,并没有谈及太多无监督学习和强化学习.在NLU领域希望RNN能够选择性地局部进行决策,之后的发展也沿着这个方向在做,包括之前流行的attention机制.但在最近的发展是向BERT这样重视预处理的方向.