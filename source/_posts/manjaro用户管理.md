---
title: manjaro用户管理
date: 2021-01-02 08:19:53
categories: 
tags: manjaro
mathjax: true
---
重新在manjaro上部署大数据组件，尝试把原来没有整理清楚的用户管理梳理一下。

<!--more-->
首先Linux的用户信息都在/etc/passwd文件中，可以看到相应的用户和用户组

安装postgresql后会自动创建postgres用户，需要
1. 先删除postgres用户的密码

```bash
    sudo passwd -d postgres
```

2. 设置新的密码

```bash
    sudo -u postgres passwd
```

3. 输入两次新的密码