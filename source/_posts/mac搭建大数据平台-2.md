---
title: mac搭建大数据平台(2)-hive
date: 2020-07-20 11:56:03
categories: big data
tags: big data
mathjax: true
---
hive的配置比较简单，在mac上配置postgresql作为元数据库

在/usr/local/Cellar/hive/{version}/conf目录下

    cp hive-default.xml.template hive-site.xml

主要配置以下项目

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

最后使用schematools格式化metasotre的schema

    schematool -dbType postgres -initSchema

