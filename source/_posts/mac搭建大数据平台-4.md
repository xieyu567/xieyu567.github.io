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

<!--more-->

将postgresql的jdbc包放到{sqoop}/lib文件夹中，
测试连接
```bash
    sqoop list-databases --connect jdbc:mysql://127.0.0.1:5432/ --username root -P
```

遇到的问题：
使用命令将数据从hive导出到postgresql
```bash
    bin/sqoop export --connect jdbc:postgresql://localhost:5432/xieyu --username **** --password **** --table user_log --export-dir '/user/hive/warehouse/dbtaobao.db/inner_user_log' --fields-terminated-by ',' --lines-terminated-by '\n';
```
会出现报错
```bash
    java.lang.Exception: java.io.IOException: java.lang.ClassNotFoundException: user_log
```
因为找不到生成的jar包位置
解决方法：
1. 将/tmp/sqoop-****/compile内相应表名的.class，.jar，.java包拷贝到{sqoop}/libexec/lib下
2. 在执行命令后加参数--bindir {sqoop}/libexec/lib