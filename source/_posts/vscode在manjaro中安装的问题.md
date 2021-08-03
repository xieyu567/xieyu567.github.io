---
title: vscode在manjaro中安装的问题
date: 2021-08-03 14:09:54
categories: 
tags: manjaro
mathjax: true
---
首先使用的是官方源的vscode,版本号为1.58.0-1，应该是有问题，无法连接商店也无法同步设置。github的解决方法是设置代理，但是实际操作后发现并没有什么用。

接下来直接从官方网站获取tar包，直接使用发现网络是可以通的，能够连接商店和登陆帐号。但是登陆github时会出现错误"writing login information to the keychain failed"。这里需要安装钥匙环组件。
```bash
    sudo pacman -S gnome-keyring libsecret
```

最后在npm安装package.json时出现错误，需要把package-lock.json删除后安装，最后安装hexo-cli就可以在新电脑上写blog了。
