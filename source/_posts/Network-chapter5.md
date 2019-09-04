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

#### 5.9
&emsp;&emsp;已知延迟带宽积为$100*10^{-3}s*1*10^9bps=10^8b=12.5MB$,因此接收端的窗口大小为24(因为$2^{24}=1.67*10^7$,并且报文段最小为1字节).

&emsp;&emsp;SequenceNum用来防止在最大段生存期内回绕.在段最大生存期能够发送$30s*1Gbps=3.75GB$的数据,因此需要32位($2^{32}=4.3G$).

#### 5.12
a)TCP的序号字段有32位,也就是在达到回绕之前能够发送$2^{32}B$数据,因此需要$2^{32}B/(10^9bps/8)=34s$

b)在回绕时间内增量1000次,就是每34ms增量一次.

#### 5.14
a)如果是重传,那么前后两个包的序号应该是一致的,如果是主机B崩溃重启,那么后发送的包序号和之前的不同.因为初始建立TCP连接时初始序号是随机选择的.

#### 5.16
a)伪造的分组发送到主机A后,A会返回分组给B,但是由于ACK+1,B识别这是个无效的分组后,发送RST给A,重置TCP连接.