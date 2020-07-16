---
title: mac搭建大数据平台
date: 2020-07-16 21:51:36
categories: big data
tags: big data
mathjax: true
---
因为用不了台式机，只能在mac上装大数据组件玩玩，但是遇到的问题很多，做一下记录，防止忘记-_-!

常用的大数据组件都可以用brew安装，包括hadoop，spark，flink等

```bash
    brew install hadoop
    brew install apache-spark
    brew install apache-flink
```

需要注意的点
1. ssh需要免密登录

```bash
    ~/.ssh/id_rsa.pub >> ~/.ssh authorized_keys
```

1. secondary namenode配置。因为安装了IDEA，所以0.0.0.0与secondary namenode的默认值冲突，会显示
   
```bash
   secondary namenodes [account.jetbrains.com]
```

解决方法是在hdfs-site.xml中添加配置项

```bash
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>localhost:9868</value>
    </property>
```

另外在伪分布模式中需要将dfs.replication的值改为1，默认值为3。

1. 修改JAVA_HOME

```bash
   /usr/libexec/JAVA_HOME -V
```

查看现有的jdk路径，将jdk1.8路径复制到hadoop-env.sh

```bash
    export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_231.jdk/Contents/Home
```

1. hdfs安全模式

```bash
    hdfs dfsadmin -safemode leave/get/enter
```

参数分别是退出safemode，取得safemode状态和进入safemode。

1. 错误提示的消除方法
   
```bash
    WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

这个提示不会影响运行，但是很烦，需要用源码编译出native-hadoop library。但是试了几次都没有成功，暂时先搁置。

默认端口：
1. namenode：localhost:9870
2. ResourceManager: localhost:8088
3. Flink: localhost:8081
4. Spark: localhost: 8080