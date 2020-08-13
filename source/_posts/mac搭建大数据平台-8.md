---
title: mac搭建大数据平台-8
date: 2020-08-13 08:14:34
categories: big data
tags: big data
mathjax: true
---
使用brew安装
```bash
brew install kafka
```
这里使用kafka自带的zookeeper

默认命令和端口:
1. zookeeper-server-start {kafka}/config/zookeeper.properties #在启动kafka之前需要先启动zookeeper
2. kafka-server-start {kafka}/config/server.properties #启动kafka
3. kafka-topics --create --zookeeper localhost:2181 --replication -factor 1 --partitions 1 --topic {topicName} #新建topic
4. kafka-console-producer --broker-list localhost:9092 --topic {topicName} #producer生产数据，在>后可以输入需要的数据，ctrl+c退出即可
5. kafka-console-consumer --bootstrap-server localhost:9092 --topic {topicName} --from-beginning #在consumer端读取数据
6. localhost:2181 #zookeeper端口
7. localhost:9092 #kafka端口
8. zookeeper-server-stop kafka-server-stop #停止服务