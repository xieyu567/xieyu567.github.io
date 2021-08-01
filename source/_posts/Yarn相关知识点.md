---
title: Yarn相关知识点
date: 2021-08-01 10:12:15
visitors: 
mathjax: true
tags: 面试
---

## Yarn工作机制
Yarn由ResourceManager、NodeManager、 ApplicationMaster和Container等组件组成。
其中ResourceManager由Scheduler和Applications Manager组成。NodeManager是节点上的任务和资源管理器。ApplicationMaster每个程序拥有一个。Container是资源调度的单元。各组件通过RPC通信。
1. client向Yarn提交应用程序。
2. client获取一个YarnRunner向ResourceManager申请一个Application。
3. ResourceManager将资源路径返回给YarnRunner。
4. client将运行所需的资源提交到Yarn上，包括MRAppMaster程序、MRAppMaster启动脚本程序、用户程序等。
5. 应用程序作为一个Task放置在Schedular中，ResourceManager为提交的程序选择一个空闲的NodeManager分配一个Container。并与相应的NodeManager通信，要求NodeManager在当前Container中运行MRAppMaster。
6. MRAppMaster启动后向ResourceManager注册自己。
7. 通过轮询方式向ResourceManager申领资源。
8. MRAppMaster向ResourceManager申请Task需要的资源，ResourceManager分配相应NodeManager给MRAppMaster，MRAppMaster将程序脚本发给ResourceManager，ResourceManager开始启动脚本，开始MapTask。
9. MapTask结束后，MRAppMaster向ResourceManager申请资源开始ReduceTask。
10. 全部结束后，MRAppMaster向ResourceManager注销自己。

## Yarn的调度器
1. FIFO scheduler：先进先出，默认。
2. Capacity scheduler：通过计算分配资源使得最大化吞吐量和集群利用率。
3. Fair scheduler：每个任务分配相同的资源。

## Container的理解
1. Container作为资源分配和调度的基本单位，封装了内存和CPU。
2. Container由ApplicationMaster向ResourceManager申请，由ResourceManager中的TaskScheduler异步分配给ApplicationMaster。
3. Container的运行由ApplicationMaster向资源所在的NodeManager发起。