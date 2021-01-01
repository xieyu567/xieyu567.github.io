---
title: kde假死事件
date: 2021-01-01 18:53:03
categories: 
tags: manjaro
mathjax: true
---
又开始折腾manjaro了，从xfce换到了kde，但是下午出现了两次假死的情况，总结了解决方法。

<!--more-->

如果还可以在输入，那么
1. alt+F2
2. kquitapp5 plasmashell && plasmashell &

如果已经无法输入了，那么切换到tty2
1. ctrl+alt+F2
2. who (查看kde的显示号，一般为0)
3. DISPLAY=:0 plasmashell --replace &