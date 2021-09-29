---
title: manjaro内核读取错误
mathjax: true
date: 2021-09-29 21:17:07
categories: 
tags: manjaro
---
记录一个比较常见的错误，可能出现在pacman更新之后
Boot: failed to start load kernel module
<!--more-->

一般使用LiveUSB启动，在终端中输入

```bash
    su
    manjaro-chroot -a
    pacman -Syu grub
```

之后重启就行了

其中chroot是用来变更当前进程的根目录，一般是用来进行系统维护的，比如重设密码，重置引导程序等，因此下一个命令就是更新grub引导，让manjaro能够正常启动。