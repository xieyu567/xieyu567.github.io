---
title: scala repl问题
date: 2020-04-23 17:12:28
updated: 
categories: big data
tags: big data
mathjax: true
---
今天遇到了一个很恼人的小问题,在scala的repl中输入空格,实际上删除了一个字符,但是显示为增加空格.

在stackoverflow中发现有人通过将.pyenv目录下的infocmp取消执行权限解决这个问题,而我的系统中,infocmp在anaconda目录下,应该是与系统的infocmp冲突了,将其

    chmod a-x ./infocmp
即可.