---
title: Kafka
date: 2019-06-05 09:24:39
updated: 
categories: big data
tags: big data
mathjax: true
---
为了做流处理的实验,最近又在自己的台式机上搭建了大数据处理的环境,考虑到之前直接使用CDH的体验很差,经常会出现各种奇葩的问题,这次就自己配置各种组建.

因为之前hadoop和spark已经配置很多次了,就不再记录.现在使用的是hadoop3.2.0版本,spark使用的是2.4.3版本,为了进行流处理还需要配置kafka给spark stream喂数据.因为kafka在单机上自带脚本可以实现单节点zookeeper实例,就暂时先不配置zookeeper.

启动zookeeper的方法是:

    bin/zookeeper-server-start.sh config zookeeper.properties

然后再以守护进程方式后台启动kafka服务器:

    bin/kafka-server-start.sh -daemon config/server.properties
接下来可以创建topic:

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic topicname
创建producer发送信息:

    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topicname

还可以创建consumer从终端输出信息:

    bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topicname --from-beginning