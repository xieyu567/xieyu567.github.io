---
title: mac搭建大数据平台(7)-elasticsearch
date: 2020-08-11 23:01:41
categories: big data
tags: big data
mathjax: true
---
和之前一样用brew安装
```bash
brew install elasticsearch
```
安装kibana，一个基于node.js的数据统计工具
```bash
brew install kibana
```

默认命令和端口:
1. brew services start/stop/restart elasticsearch
2. brew services start/stop/restart kibana
3. localhost:9200 #elasticsearch
4. localhost:5601 #kibana