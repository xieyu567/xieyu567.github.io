---
title: mac搭建大数据平台(3)-hbase
date: 2020-07-20 20:55:49
categories: big data
tags: big data
mathjax: true
---
hbase的分布式配置要基于zookeeper，记录一下配置流程
用brew安装好hbase后，先配置conf/hbase-env.sh

    export JAVA_HOME={JAVA_HOME}
    export HBASE_MANAGES_ZK=true
    export HBASE_CLASS={HBASE}/conf

现在还没有装zookeeper，所以HBASE_MANAGES_ZK先设置为true，使用hbase自带的zookeeper

然后编辑conf/hbase-site.xml

    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://localhost:9000/hbase</value>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.unsafe.stream.capability.enforce</name>
        <value>false</value>
    </property>
    <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>{hbase}/tmp/zookeeper</value>
    </property>
    <property>
        <name>hbase.master.info.port</name>
        <value>60010</value>
    </property>

出现的问题
1. HMaster进程自动关闭
   需要将dfs.namenode.name.dir和dfs.datanode.data.dir里的current删除，重新执行format

默认命令和端口:
1. bin/start-hbase.sh  bin/stop-hbase.sh
2. bin/hbase shell
3. localhost:60010  #web端口需要配置