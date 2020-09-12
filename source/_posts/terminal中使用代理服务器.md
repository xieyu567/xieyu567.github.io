---
title: terminal中使用代理服务器
date: 2020-09-12 13:07:50
categories: 
tags: mac
mathjax: true
---
有时候需要在terminal中使用lantern，需要先打开lantern查看https代理服务器的地址和端口，然后在terminal中

```bash
export HTTP_PROXY=http://{ip}:{port}
export HTTPS_PROXY=http://{ip}:{port}
```

这样在当前的窗口下就可以实现代理。应该换成https也可以用