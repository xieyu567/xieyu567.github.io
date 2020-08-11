---
title: mac搭建大数据平台(6)-zeppelin
date: 2020-08-09 17:49:38
categories: big data
tags: big data
mathjax: true
---
现在用brew已经无法安装zeppelin了，需要在官网下载all interpreter package版本，直接配置之后就可以用了。

需要配置zeppelin-env.sh
```bash
export SPARK_HOME={spark}
export SPARK_CONF_DIR={spark}/conf
export HADOOP_CONF_DIR={hadoop}/etc/hadoop
```

配置zeppelin-site.xml
```bash
<property>
    <name>zeppelin.server.port</name>
    <value>9981</value>
    <description>Server port.</description>
</property>
<property>
    <name>zeppelin.notebook.dir</name>
    <value>{zeppelin}/notebook</value>
    <description>path or URI for notebook persist</description>
</property>
```

默认命令和端口:
1. bin/zeppelin-daemon.sh start  bin/zeppelin-daemon.sh stop
2. localhost:9981  #通常修改为其他端口，把默认的8080端口空出来
3. 在单元格第一行写%{类型}来定义单元格使用的interpreter，如要用markdown，第一行应该写%md