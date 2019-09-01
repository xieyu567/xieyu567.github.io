---
title: Network-chapter5
date: 2019-08-31 09:19:03
categories: Computer Network A Systems Approach
visitors: 
mathjax: true
tags: Network
---
章节五的习题:

#### 5.2
a)可以考虑这样的情况:客户端想要请求文件a,但是发出了请求文件b的数据包.随即撤回,并发送新的数据包请求文件a.但是请求数据包丢失了.最后服务器端只收到了请求文件b的数据包,并给客户端返回了文件b.

b)每次发送请求使用不同的端口号;每个数据包带上序号或者是时间戳,这样错序到达的数据包可以丢弃.

#### 5.4
&emsp;&emsp;在主机A发送给主机B数据包(Flags=FIN)后,会转移到状态FIN_WAIT_1,然后主机A会收到主机B回复的数据包(Flags=ACK+FIN).这种情况会发生在主机B在收到主机A发送的(Flags=FIN)后,立即关闭连接,并发送返回自己的FIN和从主机A收到的ACK.

&emsp;&emsp;这种情况不常见,因为一般情况是主机B一般先返回ACK,之后再返回FIN.也就是说对于主机A,状态转移的顺序是:
$ESTABLISHED\rightarrow FIN\_WAIT\_1\rightarrow FIN\_WAIT\_2\rightarrow TIME\_WAIT\rightarrow CLOSED$
对于主机B,状态转移的顺序是:
$ESTABLISHED\rightarrow CLOSE\_WAIT\rightarrow TIME\_WAIT\rightarrow CLOSED$

#### 5.5
&emsp;&emsp;