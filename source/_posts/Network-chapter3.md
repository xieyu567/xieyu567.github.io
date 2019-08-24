---
title: Network-chapter3
date: 2019-08-24 13:43:33
categories: Computer Network A Systems Approach
visitors: 
mathjax: true
tags: Network
---
章节三的习题:

#### 3.2

Exercise | Switch | Input | Output
:---: | :---: | :---: | :---:
| | |{Port, VCI} | {Port, VCI}
a) | 1 | 0&emsp;0 | 1&emsp;0
|| 2 | 3&emsp;0 | 1&emsp;0
|| 4 | 3&emsp;0 | 0&emsp;0
b) | 3 | 3&emsp;0 | 0&emsp;0
|| 2 | 0&emsp;0 | 1&emsp;1
|| 4 | 3&emsp;1 | 1&emsp;0
c) | 4 | 2&emsp;0 | 3&emsp;2
|| 2 | 1&emsp;2 | 3&emsp;1
|| 1 | 1&emsp;1 | 2&emsp;0
d) | 4 | 0&emsp;1 | 3&emsp;3
|| 2 | 1&emsp;3 | 3&emsp;2
|| 1 | 1&emsp;2 | 3&emsp;0
e) | 3 | 2&emsp;0 | 0&emsp;1
|| 1 | 0&emsp;1 | 2&emsp;0
f) | 4 | 0&emsp;2 | 3&emsp;4
|| 2 | 1&emsp;4 | 0&emsp;2
|| 3 | 0&emsp;2 | 1&emsp;0

#### 3.3

Switch | Destination | Next hip
:---: | :---: | :---:
A | B | C
|| C | C
|| D | C
|| E | C
|| F | C
B | A | E
|| C | E
|| D | E 
|| E | E
|| F | E
C | A | A
|| B | E
|| D | E
|| E | E
|| F | F
D | A | E
|| B | E
|| C | E
|| E | E
|| F | E
E | A | C
|| B | B
|| C | C
||D|D
||F|C
F|A|C
||B|C
||C|C
||D|C
||E|C

#### 3.5

一共有三条VC

1)从A到B,使用S1的1和2端口.

2)从A到D,使用S1的1和3端口,S2的1和3端口,S3的1和2端口.

3)从B到D,使用S1的2和3端口,S2的1和3端口,S3的1和2端口.

#### 3.