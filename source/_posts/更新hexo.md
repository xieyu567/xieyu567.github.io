---
title: 更新hexo
date: 2020-08-14 16:17:24
categories: 
tags: mac
mathjax: true
---
先升级hexo-cli
```bash
npm i hexo-cli -g
```

然后检查插件是否有新版本
```bash
npm install -g npm-check
npm-check
```

升级插件
```bash
npm install -g npm-upgrade
npm-upgrade
```

最后
```bash
npm update -g
npm update --save
```