---
title: Network-chapter2
date: 2019-07-24 09:45:43
categories: Computer Network A Systems Approach
visitors: 
mathjax: true
tags: Network
---
章节二的习题:

#### 2.1

&emsp;&emsp;几种编码方法的区分:

&emsp;&emsp;**NRZ**:&emsp;低电平时为0,高电平时为1.对于连续的1或0,基线漂移和时钟同步会带来问题.接收方一般是用信号的平均值作为基线判断高低电平,如果连续出现1或0,基线会变化,接收方检测信号就会出现问题;另外接收方必须与发送方的时钟完全同步才能解析NRZ信号,这样布线成本会很高,之后的编码方法通过在信号内的跳变让接收方能够恢复时钟.

&emsp;&emsp;**NRZI**:&emsp;信号为1时,进行跳变,信号为0时,不变.这种编码方式解决了连续的1的情况,但是没有解决连续的0的情况.

&emsp;&emsp;**曼彻斯特编码**:&emsp;用NRZ和时钟进行异或操作.信号为0时由低向高跳变,信号为1时由高向低跳变.因此接收方也能够很好的恢复时钟.但是因为跳变频率(波特率)加倍,曼彻斯特编码的效率只有NRZ和NRZI的一半.

&emsp;&emsp;**4B/5B编码**:&emsp;用5比特来编码4比特信号,然后使用NRZI编码传输.通过限制连续的0的数量(最多一个前导0,最多两个末尾0)来解决NRZI的问题,同时效率比曼彻斯特编码要高(80%).

#### 2.2

&emsp;&emsp;4B/5B编码为11100 01011 11110 10101

#### 2.4

&emsp;&emsp;HDLC中**比特填充**过程如下.发送方在5个连续的1后填充0(除了表示帧开始和结束的01111110),接收方在接收到5个1后分情况处理,如果后面跟着0,那么去掉0;如果后面是10,那么为帧特殊比特序列;如果后面是11,那么为无效帧,丢弃整个帧.

&emsp;&emsp;因此比特序列为1101011111<font color="red">0</font>01011111<font color="red">0</font>101011111<font color="red">0</font>110

#### 2.7

&emsp;&emsp;比特序列为01101011111<font color="red">0</font>1010011111<font color="red">1</font>10110<font color="red">01111110</font>

&emsp;&emsp;前一个0为应该移除的比特填充位,下一个1为传输错误造成的位,最后是表示帧结束的特殊比特序列.

#### 2.11

&emsp;&emsp;差错检测.最简单的二维奇偶校验.对每个7比特附加1比特的平衡1的个数,使1的个数为偶数.最后的检验字节对每个比特位进行奇偶校验.

&emsp;&emsp;出现3比特错.可能的情况有三行各出现1个比特错;一行出现2比特错,另一行出现1比特错;一行出现3比特错.第一种情况在每个校验位和检验字节都能检测到.第二种情况在检验字节能够检测和检验位能够检测到.第三种情况同样可以在检验字节和出现差错的检验位检测到.

&emsp;&emsp;二维奇偶检验能够检测所有的1,3比特错及大部分2,4比特错,例外的4比特错情况为,两行各出现2比特错,并且比特位一致,二维奇偶检验无法检测这种情况.例外的2比特错情况为,同一行出现2比特错,则在行校验中无法检查出,即使通过列校验知道出错的比特位在哪一列.

#### 2.14

&emsp;&emsp;对于两个非零数,在没有溢出的情况下,肯定不会为F0000,在溢出的情况下,需要加进位,则一定$>$F0001.

#### 2.19

&emsp;&emsp;CRC校验码的步骤,已知用于传输的多项式$C(x)$长为$k$.

1)在消息的后面扩展$k$个0

2)用得到的扩展后的消息除$C(x)$,得到余数.

3)从扩展后的消息中减去余数(等于直接和k个0异或,即把k个0替换成余数)

&emsp;&emsp;对消息1011 0010 0100 1011后面加8个0,然后除1000 00111,得余数1001 0011,最后应传输的信息为1011 0010 0100 1011 1001 0011

#### 2.23

&emsp;&emsp;a) 时延$=40km/(2*10^8m/s)=0.2ms$

&emsp;&emsp;b) 超时值$=2*EstinateRTT=0.8ms$

&emsp;&emsp;c) 因为网络可能会有拥塞等情况,仍然可能出现超时.

#### 2.25

&emsp;&emsp;$RTT=2*3*10^4km/(3*10^8m/s)=0.2s$

&emsp;&emsp;时延带宽积为$10^6bps/(10^3*8b)*0.2s=25$帧.

&emsp;&emsp;也就是在RWS=1的情况下,需要5比特作为序号.因为$MaxSeqNum\geq SWS+1$. 

&emsp;&emsp;在RWS=SWS的情况下,需要6比特,因为$SWS<(MaxSeqNum+1)/2$.