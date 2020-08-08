---
title: mac搭建大数据平台(5)-flink
date: 2020-08-08 09:58:33
categories: big data
tags: big data
mathjax: true
---
flink的安装比较简单
```bash
    brew install apache-flink
```

默认命令和端口:
1. bin/start-cluster.sh  bin/stop-cluster.sh
2. localhost:8081
3. bin/flink run {jar} #运行jar包程序