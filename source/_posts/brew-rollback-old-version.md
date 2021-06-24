---
title: brew rollback old version
date: 2021-06-24 08:03:34
categories: 
tags: mac
mathjax: true
---
由于zeppelin不支持最新的spark3.2.1，只能将其回滚到3.0.2。

1. 首先查看历史版本
```bash
brew info apache-spark
```

2. 找到历史版本，比如spark3.0.2的row为https://github.com/Homebrew/homebrew-core/blob/21b2d942b1cb03fd07d04b78129adb3d44bd2b75/Formula/apache-spark.rb

3. 删除spark
```bash
brew uninstall apache-spark
```

4. 安装指定版本
```bash
brew install https://github.com/Homebrew/homebrew-core/blob/21b2d942b1cb03fd07d04b78129adb3d44bd2b75/Formula/apache-spark.rb
```

5. 最后锁定版本，以后升级前一定要看看是否兼容-_-
```bash
brew pin apache-spark
```