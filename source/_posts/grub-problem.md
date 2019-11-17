---
title: grub problem
date: 2019-11-17 11:33:18
categories: solved problem
visitors: 
mathjax: true
tags: Manjaro
---
昨天随手在Win10里删掉了一个用户,结果进入manjaro时出现grub问题.

    unknown filesystem

按照网上的帖子,在rescue模式下,使用set查看grub设置信息.

    cmdpath=(hd0,gpt2)/MANJARO
    prefix=(hd0,gpt7)/boot/grub
    root=hd0,gpt7

重新设置grub信息

    set root=(hd0,gpt8)
    set prefix=(hd0,gpt8)/boot/grub

使用insmod normal命令保存时,提示:

    (hd0,gpt8)/boot/grub/x86_64-grub/grub.mod not found

可以改为

    insmod (hd0,gpt8)/boot/grub/x86_64-grub/normal.mod

这样就可以进入manjaro系统了,接下来先用df查看/boot/efi的挂载点,我的电脑是在/dev/nvme0n1p2,然后用命令修复:

    sudo update-grub
    sudo grub-install /dev/nvme0n1p2