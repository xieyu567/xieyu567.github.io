---
title: mlocate
date: 2021-07-25 12:22:44
categories: 
tags: manjaro
mathjax: true
---
找到了一个在linunx下很好用的文件搜索工具，直接用pacman安装
```bash
pacman -S mlocate
```

然后更新一下locate数据库，让其能够搜索全盘
```bash
sudo updatedb
```

然后就可以很方便地搜索了
```bash
locate "*.txt"
```
也可以限制搜索结果
```bash
locate -n 10 "*.txt"
```