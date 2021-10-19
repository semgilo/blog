+++
title = "android-armeabi-lib-lock"
author = ["Donghai Ruan"]
date = 2017-10-13T15:05:00+08:00
lastmod = 2021-10-19T09:43:31+08:00
tags = ["android", "cocos2dx"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

cocos2dxlua.so放在armeabi-v7a下 接SDK又引入 armeabi 几个so 结果会出现找不到cocos2dxlua.so 这个文件 导致崩溃
<!--more-->


## 问题分析 {#问题分析}

armeabi路径会搜索一次 如果找到会锁定 下次就只会在这个目录下查找


## 解决方案 {#解决方案}

删除多余的平台目录
