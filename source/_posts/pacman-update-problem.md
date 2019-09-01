---
title: pacman update problem
date: 2019-09-01 11:43:09
categories: solved problem
visitors: 
mathjax: true
tags: Manjaro
---
在使用pacman更新npm时出现问题.

    npm: /usr/lib/node_modules/npm/node_modules/socks-proxy-agent/yarn.lock exists in filesystem
    Errors occurred, no packages were upgraded.

需要用以下命令强制更新

    pacman -S package-name --overwrite '*'

用man查看pacman,可以得到--overwrite是更新(-S或-U)的参数