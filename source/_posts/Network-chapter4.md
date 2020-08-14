---
title: 'Newtwork-chapter4 '
date: 2019-08-30 10:50:18
categories: Computer Network A Systems Approach
visitors: 
mathjax: true
tags: Network
---
章节四的习题:

#### 4.1

a)总共可以收到3条路由,由1,2,3链路.

b)$A\rightarrow B$采用链路1,$B\rightarrow A$采用链路2.

c)修改路由信息,或是阻止流量通过链路1.

<!--more-->

#### 4.5
a)
Switch|Address | Next-hop 
:---: | :---:| :---:
P||
||C1.A3.0.0/16|PA
||C1.B0.0.0/12|PB
||C2.0.0.0/8|Q
||C3.0.0.0/8|R

Switch|Address | Next-hop 
:---: | :---:| :---:
Q||
||C2.0A.10.0/20|QA
||C2.0B.0.0/16|QB
||C1.0.0.0/8|P
||C3.0.0.0/8|R

Switch|Address | Next-hop 
:---: | :---:| :---:
R||
||C1.0.0.0/8|P
||C2.0.0.0/8|Q

b)同上,但是P的路由表中C3.0.0.0/8 R改为C3.0.0.0/8 Q,

R的路由表中C1.0.0.0/8 P改为C1.0.0.0/8 Q.

c)同a,除了在去掉R的路由信息,P的路由表中添加C2.0A.10.0/20 QA,

Q的路由表中添加C1.A3.0.0/16 PA

#### 4.12
IPv6中使用以11111111为前缀的地址作为多播地址. IPv4中使用D类地址作为多播地址,以1110作为前缀.