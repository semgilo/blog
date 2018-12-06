+++
title = "cocos2dx 2.2.6 file not found"
author = ["semgilo"]
date = 2018-12-03T22:03:00+08:00
lastmod = 2018-12-03T22:12:51+08:00
tags = ["cocos2dx"]
categories = ["笔记"]
draft = false
+++

今天需求弄一个旧项目，于是就是官方下了一个2.2.6的版本，下载按官方配置各种

```c++
<string> file not found
<cctype> file not found
```

各种google.

解决方法：

```c++
1.cocos2dx.xcodeproj ==> BuildSetting ==> IOS Development Target
修改为：8.0
2.bitcode 修改 NO
3.c++ Standard Library  修改为 Compiler Default
```