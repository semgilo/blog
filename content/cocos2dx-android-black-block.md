+++
title = "android etc1 黑块问题"
author = ["Donghai Ruan"]
date = 2017-05-03T03:40:00+08:00
lastmod = 2021-10-19T09:43:43+08:00
tags = ["cocos2dx", "android"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

在SDK返回的时候，部分UI会出现黑块，LOG会提示Opengl GL 502等错误


## 问题分析 {#问题分析}

一开始我的处理方案是把那些异常的资源重新加载，是可以解决大部分问题，但觉得这并不上策。于是我再继续找根源。。。
根源是因为SDK在初始化|登录|支付的回调中去可能去调用到我们的UI，这个回调的线程并不是\*Cocos2dxUI线程\*，这样子操作UI是不能保证先后顺序，这个时候android机子会触发重新加载资源的时机。
所以部分重载一半或者一些不可预料的黑块就出现了。


## 解决方案 {#解决方案}

```lua
function ()
    xxx:runAction(cc.Sequence:create(
        cc.DelayTime:create(0.02),
        cc.CallFunc:create(function ()
            -- ... do something
        end)))
end
```
