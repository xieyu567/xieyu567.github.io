---
title: mac搭建大数据平台(2)-hive
date: 2020-07-20 11:56:03
categories: big data
tags: big data
mathjax: true
---
hive的配置比较简单，在mac上配置postgresql作为元数据库

在/usr/local/Cellar/hive/{version}/conf目录下
```bash
    cp hive-default.xml.template hive-site.xml
```

<!--more-->

主要配置以下项目
```bash
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:postgresql://{hostname}:5432/{hivedatabase}?createDatabaseIfNotExist=true</value>
    
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>{hivepassword}</value>

    <name>javax.jdo.option.ConnectionUserName</name>
    <value>{hiveusername}</value>

    <name>hive.metastore.schema.verification</name>
    <value>false</value>

    <name>hive.exec.local.scratchdir</name>
    <value>{hivedir}/tmp<value>

    <name>hive.download.resources.dir</name>
    <value>{hivedir}/tmp/resources</value>

    <name>hive.querylog.location</name>
    <value>{hivedir}/tmp</value>

    <name>hive.server2.logging.operation.log.location</name>
    <value>{hivedir}/tmp/operation_logs</value>
```

最后使用schematools格式化metasotre的schema
```bash
    schematool -dbType postgres -initSchema
```

启动hiveserver2服务后，使用beeline命令连接hiveserver2
```bash
    !connect jdbc:hive2://localhost:10000
```

需要注意的点：
1. hive不允许匿名用户访问，错误信息如下
 ```bash  
   Error: Could not establish connection to jdbc:hive2://127.0.0.1:10000: Required field 'serverProtocolVersion' is unset!
```

需要修改hadoop的配置文件core-site.xml
```bash
    <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
    </property>
```

2. 关闭hive服务。直接kill掉jps显示的jar进程


默认命令和端口:

1. nohup hiveserver2 & #后台启动hive服务器
2. hiveserver2: localhost:10002
3. beeline #现有版本已用beeline命令取代hive cli命令