---
title: Spark相关知识点
date: 2021-07-31 16:18:22
visitors: 
mathjax: true
tags: 面试
---

## Spark模型工作类型
1. 本地模式
2. StandAlone模式
3. Spark on Yarn模式，分为Yarn-client和Yarn-cluster
4. Spark on Mesos模式
<!--more-->
---
本地模式只有一个SparkSubmit进程，把任务全部包圆。

---
StandAlone模式构建一个Master和Slave构成的集群，资源管理和任务监控是Spark自己监控。
StandAlone模式包含Master进程（进行资源管理）、SparkSubmit进程（作为Clinet端和运行driver程序）以及CoarseGrainedExecutorBackend进程（并发执行应用程序）。
StandAlone模式主要节点有Client节点、Master节点和Worker节点。

StandAlone运行流程：
1. SparkContent连接到Master，向Master注册并申请资源。
2. Master根据SparkContext的资源申请要求和Worker心跳周期内报告的信息决定在哪个Worker上分配资源，然后在该Worker上获取资源，然后启动StandaloneExecutorBackend。
3. StandaloneExecutorBackend向SparkContext注册。
4. SparkContext将Application代码发送给StandaloneExecutorBacked。SparkContext解析Application代码，构建DAG图，并提交给DAG Scheduler分解成Stage，DAG Scheduler将TaskSet提交给Task Scheduler，Task Scheduler将task分配到相应的Worker，最后提交给StandaloneExecutorBackend执行。
5. StandaloneExecutorBackend建立Executor线程池，开始执行task，并向SparkContext报告，直至task完成。
6. 所有task完成后，SparkContext向Master注销自己。

---
Spark on Yarn模式将集群部署、资源和任务监控交给Yarn管理，仅支持粗粒度资源分配方式。

Yarn-client和Yarn-cluster的区别：
* Yarn-client适用于调试环境。driver运行在客户端，负责调度application，会与Yarn集群产生大量的网络通信。因为本地执行，因此能够看到所有logs，方便调试。ApplicationMaster仅向Yarn请求Executor，功能十分有限。
* Yarn-cluster适用于生产环境，driver运行在集群子节点的ApplicationMaster中，负责向Yarn申请资源，并监督作业的运行状况。

Yarn-client运行流程：
1. Spark Yarn Client向Yarn的ResouceManager发送请求，申请启动ApplicationMaster。同时在SparkContext初始化中创建DAGScheduler和TaskScheduler。
2. ResourceManager收到请求后，在集群选择一个NodeManager，为应用程序分配第一个Container，在Container中启动应用程序的ApplicationMaster。（这个ApplicationMaster不运行SparkContext，只启动ExecutorLanucher）
3. client中的SparkContext初始化完毕后，与ApplicationMaster建立连接，向ResourceManager注册，根据任务信息向ResourceManager申请Container。
4. ApplicationMaster申请到Container后，与相应的NodeManager通信，在Container中启动CoarseGrainedExecutorBackend，CoarseGrainedExecutorBackend启动后向Client中的SparkContext注册并申请task。
5. client中的SparkContext分配task给CoarseGrainedExecutorBackend执行，CoarseGrainedExecutorBackend运行task并向driver汇报运行状态和进度。
6. 程序完成后，client的SparkContext向ResourceManager申请注销并关闭。

Yarn-cluster运行流程：
1. Spark Yarn Client向Yarn的ResouceManager发送请求，申请启动ApplicationMaster。
2. ResourceManager收到请求后，在集群选择一个NodeManager，为应用程序分配第一个Container，在Container中启动应用程序的ApplicationMaster。（这个ApplicationMaster相当于driver客户端，进行SparkContext初始化）
3. ApplicationMaster向ResourceManager注册。
4. ApplicationMaster申请到Container后，与相应的NodeManager通信，在Container中启动CoarseGrainedExecutorBackend，CoarseGrainedExecutorBackend启动后向Client中的SparkContext注册并申请task。
5. ApplicationMaster中的SparkContext分配task给CoarseGrainedExecutorBackend执行，CoarseGrainedExecutorBackend运行task并向ApplicationMaster汇报运行状态和进度。
6. 程序完成后，ApplicationMaster向ResourceManager申请注销并关闭。

## Spark on Yarn的优点
1. 与其他计算框架共享集群资源。
2. 比Spark自带的Standalone模式资源分配更加细致。
3. Application部署简化。
4. Yarn管理集群中的多个服务，能根据负载情况，调整资源使用。

## StandAlone模型的优缺点
优点：部署简单，不依赖其他资源管理系统。
缺点：默认每个应用程序会独占所有可用节点的资源，设置项为spark.cores.max；可能存在单点故障，需要自己配置master HA。

## Spark比MapReduce快的原因
1. IO操作：MapReduce每次Shuffle都要写入磁盘，Spark的Shuffle可以缓存到内存。
2. Shuffle机制：每次MapReduce都会有一次Shuffle，Spark只有碰到宽依赖才会Shuffle。
3. JVM优化：MapReduce是以进程的方式运行在Yarn集群中，有多少个task就要开启多少个进程，每启动一个task就会启动一次jvm。Spark是以线程的方式运行的，只在启动Executor进程时启动一次jvm，Executor进程中维护着一个线程池。节省了大量jvm启动时间。

## Spark Shuffle的具体流程
有两种模式：HashShuffleManage和SortShuffleManage，其中SortShuffleManage是默认模式。
* HashShuffleManage：在execute中处理每个task后的结果通过bucket缓存的方式写入多个磁盘文件中，reduce task个数由shuffle算子的numPartition参数指定。比如有两个execute分别处理两个task，当numPartition设置为3时，会产生2\*2\*3个文件。<br/>
优化方法：executor处理多个task时，处理完第一个task产生numPartition个文件，之后的task结果追加到相应的这numPartition个文件中，也就是每一个task产生的结果都共用用一个bucket，这个文件称作ShuffleBlockFile。这样只会产生2（execute）\*3（numPartition）个文件。把spark.shuffle.consolidateFiles设置为true就能完成以上优化机制。

* SortShuffleManage：
1. 在内存中通过溢写的方式将结果写入磁盘，在溢写前，有两个重要操作：
    1. 数据聚合，数据会先写入一个内存数据结构，如果是reduceByKey这种聚合类算子，会选用Map,一边通过Map聚合，一边写入内存。如果是join这种普通的shuffle算子，会选用Array,直接写入内存。
    2. 数据溢写到磁盘前会进行排序操作。
2. 对磁盘文件进行合并，通过索引文件标注key值在文件中的位置。
产生的文件数为reduce task数\*2
针对不适宜排序的情况也可以使用bypass模式，和前面一样，只是去掉排序操作，通过设置spark.shuffle.sort.bypassMergeThreshold可以达到这一目的，默认值为200，如果map task数小于该值或者reduce是非聚合操作，就会启用bypass模式，否则是普通模式。

## fetch处理时机
Spark默认情况下是不会对数据进行排序的，因此Shuffle Write的executor每写入一点数据，Shuffle Read的executor就可以拉取。

## Spark优化
1. 平台层面：防止不必要的jar包分发；提高数据的本地性；选择高效的存储格式parquet；资源参数调优（num-executors设置executor数量；executor-memory设置每个executor可申请内存；executor-cores设置每个executor进程的CPU core数量；spark.default.parallelism设置每个stage的默认task数量，一般设置为executor-cores\*num-executors的2-3倍；spark.storage.memoryFraction设置RDD持久化数据在executor内存中能占的比例；spark.shuffle.memoryFraction设置shuffle过程中进行聚合时能够使用的executor内存比例）。
2. 应用程序层面：优化过滤操作符，防止过多、过小的任务；降低单条语句的资源开销；任务并行化；复用RDD进行缓存；对多次使用的RDD持久化；处理数据倾斜；尽可能避免使用shuffle算子；使用map-side预聚合（有点类似与MapReduce中的Combine，在本地对相同的key进行聚合）；使用高性能算子（reduceByKey/aggregateByKey代替groupByKey，mapPartitions代替map，foreachPartitions代替foreach，filter之后进行coalesce操作，repartitionAndSortWithinPartitions代替repartition和sort类操作）。
3. JVM层面：启用高效的序列化方法kyro，增大off head内存。

## Spark持久化级别
1. MEMORY_ONLY：默认选项，RDD数据以Java对象形式存储到JVM内存中。如果内存不足，一些数据将不会被缓存。
2. MEMORY_AND_DISK：RDD数据以Java对象形式存储到JVM内存中。如果内存不足，一些数据将存储在磁盘。
3. MEMORY_ONLY_SER：RDD数据序列化后存储到JVM内存中。如果内存不足，一些数据将不会被缓存。比MEMORY_ONLY节约内存空间，但是读取时需要更多CPU开销。
4. MEMORY_AND_DISK_SER：可以从上面推出意思^_^。
5. DISK_ONLY：只使用磁盘存储RDD数据。
6. MEMORY_ONLY_2，MEMORY_AND_DISK_2...：在以上后面加上2，表示在其他节点保存一个备份，用于容灾备份。
使用cache()进行默认缓存或是使用persist(StorageLevel.MEMORY_ONLY)来指定持久化级别。

## parquet格式的好处
速度更快；压缩技术稳定；扫描的吞吐量大；优化Spark的调度和执行。

## RDD缺陷
1. 不支持细粒度的写和更新操作，只能批量写。
2. 不支持增量迭代计算。

## DataFrame比RDD性能好
1. DataFrame会自动经过Spark优化器（Catalyst），查询计划得到优化。
2. 使用Off-heap，由操作系统管理的内存，避免大量GC。

## RDD操作
有两种：Transformation和Action
* Transformation
接收RDD，返回一个或多个新的RDD，不改变输入。
Transformation是lazy操作，执行Action操作时，才真正执行。
其中父子RDD是一对一对应关系的为窄依赖，一对多对应关系的为宽依赖。
* Action
将最终结果返回给Driver或写入外部数据存储。
First()，take()，reduce()，collect()，count()是常见的一些Action。

## Spark内存划分
Spark把堆内内存划分成两个区域：
1. Execution Memory，用于执行分布式任务，如Shuffle、Sort和Aggregate等。
2. Storage Memory，用于缓存RDD和广播变量等数据。
Spark还会在堆内划分出User Memory的内存空间，用于存储开发者自定义数据结构。还有一块Reserved Memory，默认300MB，用来存储各种Spark内部对象，如存储系统中的BlockMemory、DiskBlockMemory等。
![Spark内存管理](https://tva1.sinaimg.cn/large/008i3skNgy1gt26qnv2zuj30y50e4jtn.jpg)
Spark2之后默认使用的统一内存管理模式，设置项为spark.memory.useLegacyMode。Execution Memory和Storage Memory可以互相借用对方的内存。两者之间的抢占规则有3条。
1. 如果对方的内存空间有空闲，双方都可以抢占。
2. 对于RDD缓存任务抢占的Execution Memory，当任务有内存需要时，RDD缓存任务必须立即归还抢占的内存，涉及的RDD缓存数据要么写入磁盘，要么清除。
3. 对于分布式计算任务抢占的Storage Memory，即使RDD缓存任务有收回内存的需要，也要等到任务执行完毕才能释放。

## Spark堆外内存
由spark.memory.offHeap.size指定堆外内存的大小，通过直接操作系统堆外内存，减少不必要的内存开销已经频繁的GC扫描和回收，提升处理性能。

## Spark数据本地化级别
1. PROCESS_LOCAL，进程本地化，表示task要计算的数据在同一个Executor中。
2. NODE_LOCAL，节点本地化，表示task要计算的数据在同一个节点的不同的Executor中、数据在同一节点的磁盘上、或是HDFS上恰好有块在同一节点上。比如Spark要计算的数据在HDFS上。
3. NO_PREF，没有最佳位置。比如Spark从数据库中读取数据。
4. RACK_LOCAL，机架本地化，数据在同一机架的不同节点上，需要网络传输或是IO。
5. ANY，跨机架，最慢。
TaskScheduler发送task时也是以这个优先级来确定数据的位置，先向Executor发送task，等待一段时间后无法执行，就会降低数据本地化级别，发送task给同一节点的其他Executor...
通过spark.locality.wait设置等待时间。

