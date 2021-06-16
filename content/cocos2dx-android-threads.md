+++
title = "cocos2dx android ui and gl threads"
author = ["Donghai Ruan"]
date = 2017-05-19T01:18:00+08:00
lastmod = 2021-06-16T10:18:16+08:00
tags = ["cocos2dx", "android"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

搞android SDK 的时候，因为接quickSDK没有lua版本，只好用android 原生版本，自己写 java跟lua的交互
java 跟 lua 交互有概率性的会出现闪退


## 问题分析 {#问题分析}

这种概率性的闪退，一般都是线程不安全导致的，于是我就去找，看到cocos2dx 里面有很多用到

```java
mActivity:runOnUiThread(new Runnable(){
     public void run() {
     // xxoo xxoo
     }
})
```

然后我就认为这个是cocos2dx的主线程，然后就拼命的用它去调用lua,结果就是闪退不断，
这个时候我觉得是不对的，如果这个是主线程是不可能有这个问题，于是我再去找，发现一个方法长得跟它很像的

```java
mActivity:runOnGLThread(new Runnable(){
     public void run() {
     // xxoo xxoo
     }
})
```

这时候我才清楚，原来这个GL线程才是我们游戏线程，UI线程是UI框架的，如果调用高级UI或者SDK就需要用UI线程。


### 解决方案 {#解决方案}


## 解决方案 {#解决方案}

cocos2dx的东西使用 GL线程
android高线UI使用 UI线程
