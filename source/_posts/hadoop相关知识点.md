---
title: hadoop相关知识点
date: 2021-06-24 17:07:38
visitors: 
mathjax: true
tags: 面试
---

##Mapreduce
一种计算框架，分为input,split,map,shuffle,reduce,finalize六个步骤

举个例子：厨房购入一些水果（input），不同的厨师拿取水果（split），将水果切好（map），放入不同的容器中（shuffle），根据需求从不同的容器中取出组合成果盘（reduce），等待顾客消费（finalize）。

以常见的wordcounts来说明：
![wordcounts-Mapreduce示例](https://tva1.sinaimg.cn/large/008i3skNgy1grtgzic9qhj31g90irq4n.jpg)

##各进程的功能
1. DataNode负责文件的存储和读写操作。
2. NameNode负责整个分布式文件系统的元数据管理，包括文件路径名、数据块ID以及存储位置等信息。
3. Secondary NameNode定期与NameNode进行通信，定期保存元数据的快照。
4. JobTracker负责分配task，并监控运行的task。
5. TaskTracker负责具体的task，与JobTracker进行交互。

##YARN任务流程
1. Client向Yarn提交任务，包括MapReduce Application Master、MapReduce程序以及MapReduce Application启动命令。
2. Resource Manager分配资源，开启一个Container，在其中运行Application Manager。
3. Application Manager找一台Node Manager启动Application Master，计算所需的资源。
4. Application Master向Application Manager申请需要的资源。
5. Resource scheduler将资源封装给Application Master。
6. Application Master将资源分配给各个Node Manager。
7. 各个Node Manager执行完任务后将结果反馈给Application Master。
8. Application Master将结果反馈给Application Manager，并注销进程以及释放资源。
其中Application Manager、Resource Manager、Resource scheduler都属于YARN