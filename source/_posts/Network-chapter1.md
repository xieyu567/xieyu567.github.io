---
title: Network-chapter1
date: 2019-07-23 09:59:02
categories: Computer Network A Systems Approach
visitors: 
mathjax: true
tags: Network
---
章节一的习题:

#### 1.1

&emsp;&emsp;匿名ftp有三种方法:
1. 用户名为anonymous,密码为空
2. 用户名为FTP,密码为空或FTP
3. 用户名为USER,密码为pass

&emsp;&emsp;majaro可以使用fileZilla这个ftp工具. 题中的ftp站点结构和题目描述不符, 如果需要RFC规范可以在IETF(Internet Engineering Task Force)获取. https://www.ietf.org/rfc/

#### 1.2

&emsp;&emsp;whois用于查询域名和组织,例如查询google的域名信息,可以使用两种方法:
```bash
whois google
whois google.com
```

#### 1.3

1. &emsp;$2\times RTT+1000KB/1.5Mbps+RTT/2\approx 5.458s$ 
   
   第一项是题目给出的初始握手阶段的时延,第二项是传输的时延$Size/Bandwidth=8Mb/1.5Mbps\approx 5.333s$.第三项是单程的传播时延.
2. 在上一题的基础上再加上999个RTT, $5.458s+RTT*999=55.408s$
3. 一共需要49.5个RTT(最后一次传输只用0.5RTT),加上初始时延,$(49.5+2)*RTT=2.575s$
4. 一共需要9.5个RTT,因此为$(9.5+2)*RTT=0.575s$

#### 1.4

