---
title: Hadoop相关知识点
date: 2021-08-06 12:54:23
visitors: 
mathjax: true
tags: 面试
---

## Mapreduce流程
一种计算框架，分为input,split,map,shuffle,reduce,finalize六个步骤

举个例子：厨房购入一些水果（input），不同的厨师拿取水果（split），将水果切好（map），放入不同的容器中（shuffle），根据需求从不同的容器中取出组合成果盘（reduce），等待顾客消费（finalize）。
<!--more-->
以常见的wordcounts来说明：
![wordcounts-Mapreduce示例](https://tva1.sinaimg.cn/large/008i3skNgy1grtgzic9qhj31g90irq4n.jpg)

## Hadoop各进程的功能
1. DataNode负责文件的存储和读写操作，定期向NameNode发送心跳。
2. NameNode负责整个分布式文件系统的元数据管理，包括文件路径名、数据块ID以及存储位置等信息。
3. Secondary NameNode定期与NameNode进行通信，定期保存元数据的快照，辅助NameNode对fsimage和editsLog进行合并。
4. JobTracker负责分配task，并监控运行的task。
5. TaskTracker负责具体的task，与JobTracker进行交互。

## MapReduce负载均衡
1. 数据均衡服务要求NameNode生成DataNode数据分布分析报告，获取每个DataNode磁盘使用情况。
2. 数据均衡服务汇总情况，通过网络拓扑和数据使用情况，确定数据迁移路线图。
3. Proxy Source DataNode复制一个数据块到目标DataNode，并在Source DataNode删除相应数据块。
4. 目标DataNode向Proxy Source DataNode确认数据块迁移完成。
5. Proxy Source DataNode向数据均衡服务确认数据块迁移完成，重复以上步骤，直至集群达到负载均衡。

{Hadoop_Home}/bin/start-balance.sh为启动脚本
参数-threshold可以设置阈值，默认为10，也就是阈值在10%以内集群都算均衡。

## HDFS中的block、packet、chunk
1. block一般是128MB，一般不做修改，原因有两点，一是block设置太大，更多的时间会浪费在传输数据块上，二是block设置太小，NameNode需要存储更多的元数据，大量的数据块和元数据信息会造成网络阻塞。
2. packet默认是64KB，是client向DataNode或DataNode之间Pipeline传输数据的基本单位。
3. chunk默认是512B，是数据校验的基本单位。每个chunk还需要携带4B的校验位，因此每个chunk写入packet实际值为516B。

在client向DataNode传输数据时，HDFSOutputStream有一个chunk buffer，写满一个chunk容量时，会校验再写入当前chunk，然后带校验位的chunk写入packet。packet将传输到其他DataNode并存储。

## HDFS写流程
1. client向NameNode发出写请求。
2. 检查权限，直接将操作写入EditLog（WAL）
3. client将文件分成数据块。
4. client将NameNode返回的可写DataNode列表和数据块发往第一个DataNode，由此client和可写DataNode形成Pipeline，每个packet通过pipeline从client流到第一个DataNode，然后流到第二个DataNode...
5. 每个DataNode写完一个数据块会返回确认消息。
6. 写完数据，关闭Pipeline。
7. 完成任务，返回给NameNode。

## HDFS读流程
1. client向NameNode发出读请求。
2. 获得数据块所在DataNode列表，就近与DataNode建立数据流。
3. 传输，以packet为单位校验。
4. 完成任务，关闭数据流。

## fsimage和editlogs的机制
fsimage保存着hadoop的元数据信息，如果NameNode发生故障，最近的fsimage会被载入内存，用来重构元数据的最近状态，再从相关点开始向前执行editlogs文件中记录的事务。

如果editlogs太大的话，重建NameNode会很慢，因此NameNode运行时会定期合并fsimage和editlogs。

## NameNode工作机制
1. 第一次启动NameNode需要创建Fsimage和Editlogs，如果不是，则直接加载这两个文件到内存中。
2. client对元数据申请操作请求。
3. NameNode先将操作记录到Editlogs，再对元数据进行操作。
4. Secondary NameNode询问NameNode是否需要CheckPoint，并返回消息。
5. Secondary NameNode请求执行CheckPoint。
6. NameNode切割现有Editlogs，新操作滚动写入到新的Editlogs中。
7. 滚动前的Editlogs拷贝到Secondary NameNode。
8. Secondary NameNode将Editlogs和Fsimage加载到内存合并。
9. 生成新的Fsimage.chkpoint后拷贝到NameNode。
10. NameNode将Fsimage.chkpoint重命名为Fsimage。

## DataNode工作机制
1. 一个数据块在DataNode存储包括两个文件：一是数据本身，二是元数据包括数据块长度，块数据的校验和以及时间戳。
2. DataNode启动后向NameNode注册，并周期性（默认1小时）地向NameNode上报所有的块信息。
3. 每3秒一次心跳，心跳返回NameNode给DataNode下达的文件操作指令。如果超过10分钟没有收到DataNode心跳，则判定该DataNode不可用。

## Hadoop的设计缺陷
1. 不支持并发写入和对文件内容的修改，client获得NameNode允许写的许可后，数据块会加锁直至写入完成，因此不能同时在一个数据块上进行写操作。只适合一次写入、多次读取的场景。
2. 不支持低延迟、高吞吐的数据访问。
3. 不适合大量小文件的存储。会占用大量的NameNode内存资源。

## Hadoop的配置文件
1. core-site.xml。主要配置项有：
* fs.defaultFS，设置默认hdfs路径。
* hadoop.tmp.dir，设置默认NameNode、DataNode、Secondary NameNode数据存放路径。
* ha.zookeeper.quorum，设置zookeeper集群的地址和端口，设置数应为3以上的奇数。
* io.file.buffer.size，读写缓冲区的大小，默认为4KB。
2. hadoop-env.sh。主要配置项有：
* dfs.replication，设置文件块备份数，默认为3。
* dfs.data.dir，设置DataNode在本地存储的位置，默认为${hadoop.tmp.dir}/dfs/data。
* dfs.name.dir，设置NameNode在本地存储的位置，默认为${hadoop.tmp.dir}/dfs/name。
3. mapred-site.xml
* mapreduce.framework.name，设置mapreduce使用何种框架，可选值为local（默认）、classic和yarn。
4. yarn-site.xml
* yarn.nodemanager.aux-services，设置aux-services名称，默认为mapreduce_shuffle。

## Hadoop集群的进程
1. namenode。HDFS的守护进程，负责维护整个文件系统，存储整个文件系统的元数据。
2. datanode。HDFS工作节点的守护进程。
3. secondarynamenode。NameNode的冗余守护进程。
4. resourcemanager。Yarn的守护进程，负责资源调度。监控NodeManager，client的请求由其负责。
5. nodemanager。单个节点的资源管理。执行来自ResourceManager的命令。

## Hadoop实现二次排序
1. 先定义一个新的数据类型将原key和value组合成为新的key。
2. 定义一个partition，对新key的第一个字段排序，再定义一个类，对新key的第二个字段排序。
3. 这样在shuffle阶段就完成了二次排序，reduce阶段只需要遍历输出就行了。

## Mapreduce中combine和partition的作用
* combine可以减少map阶段的输出，能减少IO。举个栗子如果没有combine
"hello hello world" => map => ("hello":1, "hello":1, "world":1)
而如果有combine，则
"hello hello world" => map => ("hello":2, "world":1)
或者是要取最大值
((1, 20), (1, 10), (2, 10), (2, 1)) => map => ((1, 20), (2, 10))
可以看作是local reduce。

* partition是将map产生的kv对分配给不同的reduce task，以实现负载均衡。
举个栗子。((1, 20), (1, 10), (2, 10), (2, 1))对key值进行hash后用reduce task数取模，key为1的分配到partition1，key为2的分配到partition0。

## Hadoop Shuffle的具体流程
整体的流程可以简化为两个部分
1. map端。每个map task的结果会先存放到内存缓存区，当缓存区满了之后，会以临时文件的形式溢出到磁盘上，当整个map task结束后，再把磁盘上所有的临时文件合并，等待reduce task拉取。
2. reduce端。不断拉取当前job中每个map task的结果，并对不同DataNode拉取来的结果进行合并，最终形成reduce的输入文件。
shuffle中map端的缓冲区大小可以通过mapred-site.xml中的mapreduce.task.io.sort.mb来设置，默认是100MB。reduce端的可以通过JVM的heap size设置。

## MapReduce瓶颈与性能优化
1. 计算机性能。
2. 数据倾斜。优化方法：自定义分区，可以根据业务将需要的部分发往固定的一部分Reduce，其余的发往剩余的Reduce；使用Combine，聚合并精简Map结果；使用Map Join，避免使用Reduce Join。
3. Map和Reduce数设置不合理。如果设置的太少，就会导致task等待，延长处理时间；太多会导致Map、Reduce任务间竞争资源，导致处理超时。
4. Map时间过长，让Reduce等待。优化方法：调整mapreduce.job.reduce.slowstart.completedmaps使Map运行到一定进度，Reduce也可以开始运行，减少等待时间；规避使用Reduce；设置Reduce端的Buffer，设置mapreduce.reduce.input.buffer.percent，可以让buffer保留一部分数据供Reduce使用，而不是从磁盘读取数据。
5. 小文件太多。优化方法：预处理阶段合并小文件。用CombineTextInputFormat作为输入。
6. 大量不可分块的超大文件
7. 过多的spill和merge。优化方法：通过调整（增大）mapreduce.task.io.sort.mb和mapreduce.map.sort.spill.percent增大触发spill的内存上限，减少spill次数，减少IO；通过调整（增大）mapreduce.task.io.sort.factor增大merge的文件数目，缩短MapReduct时间。
8. IO瓶颈。优化方式：数据压缩；使用SequenceFile二进制文件。