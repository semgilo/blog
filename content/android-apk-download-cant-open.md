+++
title = "android apk 下载无法识别"
author = ["Donghai Ruan"]
date = 2017-08-09T16:15:00+08:00
lastmod = 2021-06-16T10:18:01+08:00
tags = ["android"]
categories = ["笔记"]
draft = false
+++

## 问题分析 {#问题分析}

因为游戏买广告的问题，发现转化率低的可怜，就开始找原因，用各种手机测试，发现了一个问题，就是用华为的手机下载完，在下载中心无法识别文件。
我在网页下载别人的的APK的时候会弹出打开或者保存的选项，于是我就开始找原因
<!--more-->


## 解决方案 {#解决方案}

问题一：服务器的mine配置里面type选项问题
mine type 要设置成 application/android 能下载但不能访问
mine type 要设置成 application/vnd.android.package-archive , 这样安卓默认的 浏览器 下载后可以安装.
问题二：通过CDN转换后不能弹出打开或者保存
暂没得到解决
