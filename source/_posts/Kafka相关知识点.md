---
title: Kafka相关知识点
date: 2021-08-04 22:20:49
visitors: 
mathjax: true
tags: 面试
---

## kafka副本机制
kafka的副本分为两种，Leader和Follower。每个分区在创建时会选举出一个Leader，其他的为Follower。

Follower不提供服务，只从Leader异步拉取消息，实现同步。由Leader所在的Broker处理读写操作。当Leader挂掉时，会选举出一个新的Follower。这样设计的原因一是方便实现读写一致性，二是方便实现单调读。