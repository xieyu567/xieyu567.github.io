---
title: mac搭建大数据平台(4)-sqoop
date: 2020-07-23 14:48:31
categories: big data
tags: big data
mathjax: true
---
sqoop的安装比较简单
```bash
    brew install sqoop
```

配置{sqoop}/libexec/conf/sqoop-env.sh
```bash
    export HADOOP_HOME="{HADOOP}/libexec"
    export HBASE_HOME="{HBASE}/libexec"
    export HIVE_HOME="{HIVE}/libexec"
```

将postgresql的jdbc包放到{sqoop}/lib文件夹中，
测试连接
```bash
    sqoop list-databases --connect jdbc:mysql://127.0.0.1:5432/ --username root -P
```