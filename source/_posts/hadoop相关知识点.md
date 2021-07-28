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
1. DataNode负责文件的存储和读写操作，定期向NameNode发送心跳。
2. NameNode负责整个分布式文件系统的元数据管理，包括文件路径名、数据块ID以及存储位置等信息。
3. Secondary NameNode定期与NameNode进行通信，定期保存元数据的快照，辅助NameNode对fsimage和editsLog进行合并。
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
其中Application Manager、Resource Manager、Resource scheduler都属于YARN。

##MapReduce负载均衡
1. 数据均衡服务要求NameNode生成DataNode数据分布分析报告，获取每个DataNode磁盘使用情况。
2. 数据均衡服务汇总情况，通过网络拓扑和数据使用情况，确定数据迁移路线图。
3. Proxy Source DataNode复制一个数据块到目标DataNode，并在删除相应数据块。
4. 目标DataNode向Proxy Source DataNode确认数据块迁移完成。
5. Proxy Source DataNode向Proxy Source DataNode确认数据块迁移完成，重复以上步骤，直至集群达到负载均衡。

{Hadoop_Home}/bin/start-balance.sh为启动脚本
参数-threshold可以设置阈值，默认为10，也就是阈值在10%以内集群都算均衡。

##HDFS中的block、packet、chunk
1. block一般是128MB，一般不做修改，原因有两点，一是block设置太大，更多的时间会浪费在传输数据块上，二是block设置太小，NameNode需要存储更多的元数据，大量的数据块和元数据信息会造成网络阻塞。
2. packet默认是64KB，是client向DataNode或DataNode之间Pipeline传输数据的基本单位。
3. chunk默认是512B，是数据校验的基本单位。每个chunk还需要携带4B的校验位，因此每个chunk写入packet实际值为516B。

在client向DataNode传输数据时，HDFSOutputStream有一个chunk buffer，写满一个chunk容量时，会校验再写入当前chunk，然后带校验位的chunk写入packet。packet将传输到其他DataNode并存储。

##HDFS写流程
1. client向NameNode发出写请求。
2. 检查权限，直接将操作写入EditLog（WAL）
3. client将文件分成数据块。
4. client将NameNode返回的可写DataNode列表和数据块发往第一个DataNode，由此client和可写DataNode形成Pipeline，每个packet通过pipeline从client流到第一个DataNode，然后流到第二个DataNode...
5. 每个DataNode写完一个数据块会返回确认消息。
6. 写完数据，关闭Pipeline。
7. 完成任务，返回给NameNode。

##HDFS读流程
1. client向NameNode发出读请求。
2. 获得数据块所在DataNode列表，就近与DataNode建立数据流。
3. 传输，以packet为单位校验。
4. 完成任务，关闭数据流。

##fsimage和editlogs的机制
fsimage保存着hadoop的元数据信息，如果NameNode发生故障，最近的fsimage会被载入内存，用来重构元数据的最近状态，再从相关点开始向前执行editlogs文件中记录的事务。

如果editlogs太大的话，重建NameNode会很慢，因此NameNode运行时会定期合并fsimage和editlogs。

##HDFS能不能并行写
不行，client获得NameNode允许写的许可后，数据块会加锁直至写入完成，因此不能同时在一个数据块上进行写操作。