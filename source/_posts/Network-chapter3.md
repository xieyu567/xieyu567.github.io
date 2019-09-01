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

&emsp;&emsp;一共有三条VC

1)从A到B,使用S1的1和2端口.

2)从A到D,使用S1的1和3端口,S2的1和3端口,S3的1和2端口.

3)从B到D,使用S1的2和3端口,S2的1和3端口,S3的1和2端口.

#### 3.13

&emsp;&emsp;网桥和端口之间的映射关系为
Bridge | Port
:---: | :---:
B1|C&emsp;E
B2|A&emsp;B
B3|D&emsp;E&emsp;F&emsp;G&emsp;H
B4|G&emsp;I
B5|Idle
B6|J&emsp;H
B7|A&emsp;C

#### 3.15
B1: A-interface: A; B2-interface: C
B2: B1-interface: A; B3-interface: C; B4-interface: D
B3: B2-interface: A,D; C-interface: C
B4: B2-interface: D; D-interface: D

#### 3.22
&emsp;&emsp;集线器主要的问题是冲突问题,任何主机发送的包都会在集线器处冲突.

#### 3.26
&emsp;&emsp;因为总线速度低于内存带宽,所以总线速度是瓶颈,因为每个包要经过总线两次,所以传输效率应为一半$800Mbps/2=400Mbps$,一共能处理$400Mbps/100Mbps=4$个接口.

#### 3.29
&emsp;&emsp;a) 输入队列满的时候会丢失这样的分组

&emsp;&emsp;b) 这叫做*head-of line blocking*(排头阻塞)

&emsp;&emsp;c)在输出队列中配置缓存,这样只有在输出队列满的时候才会丢失分组.

#### 3.33
&emsp;&emsp;有一种方法叫做"unnumbered point-to-point link",用于发送的端口不和其他网络中其他主机通信,其他主机也无法访问到该端口,而这个端口只能发送数据.参考RFC1812,section2.2.7.

#### 3.34
&emsp;&emsp;ipv4的offset字段为13位,而ip包的长度为$2^{16}$,因此分组的偏移应为$2^{16}/2^{13}=8$

#### 3.46
a)
||A|B|C|D|E|F
:---: | :---: | :---: | :---: | :---:| :---: | :---: | :---:
A|0|$\infty$|3|8|$\infty$|$\infty$
B|$\infty$|0|$\infty$|$\infty$|2|$\infty$|
C|3|$\infty$|0|$\infty$|1|6|
D|8|$\infty$|$\infty$|0|2|$\infty$|
E|$\infty$|2|1|2|0|$\infty$|
F|$\infty$|$\infty$|6|$\infty$|$\infty$|0

b)
||A|B|C|D|E|F
:---: | :---: | :---: | :---: | :---:| :---: | :---: | :---:
A|0|$\infty$|3|8|4|9
B|$\infty$|0|3|4|2|$\infty$
C|3|3|0|3|1|6
D|8|4|3|0|2|$\infty$
E|4|2|1|2|0|7
F|9|$\infty$|6|$\infty$|7|0

c)
||A|B|C|D|E|F
:---: | :---: | :---: | :---: | :---:| :---: | :---: | :---:
A|0|6|3|8|4|9
B|6|0|3|4|2|9
C|3|3|0|3|1|6
D|8|4|3|0|2|9
E|4|2|1|2|0|7
F|9|9|6|9|7|0

#### 3.48
Confirmed|Tentative
:---: | :---: 
(D,0,-)|
(D,0,-)|(A,8,A)
||(E,2,E)
(D,0,-), (E,2,E)|(B,4,E)
||(C,3,E)
(D,0,-), (E,2,E), (C,3,E)|(A,6,E)
||(F,9,E)
||(B,4,E)
(D,0,-), (E,2,E), (C,3,E), (B,4,E)|(A,6,E)
||(F,9,E)
(D,0,-), (E,2,E), (C,3,E), (B,4,E), (A,6,E)| (F,9,E)
(D,0,-), (E,2,E), (C,3,E), (B,4,E), (A,6,E),(F,9,E)|

#### 3.55
a)128.96.39.10和子网号128.96.39.0匹配,数据包会转发到接口0.

b)同理,128.96.40.12地址的数据包转发给R2.

c)因为子网掩码是255.255.255.128,所以对于目标地址128.96.40.151的子网号是128.96.40.128,可见路由表中没有这个子网号,所以转发到默认的R4.

#### 3.68
a)根据四个子网的主机数量,A需要128个地址,B需要64个地址,C和D需要32个地址.
则可以设计:

A的子网号为12.1.1.0/25

B的子网号为12.1.1.128/26

C的子网号为12.1.1.192/27

D的子网号为12.1.1.224/27.

b)以上的设计已经可以容纳32的主机数量,如果再增加主机可以通过网桥连接,或者是重新划分子网的方式.