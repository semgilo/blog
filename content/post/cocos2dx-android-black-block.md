+++
title = "android etc1 黑块问题"
author = ["semgilo"]
date = 2017-05-03T03:40:00+08:00
lastmod = 2018-11-16T09:45:54+08:00
tags = ["cocos2dx", "android"]
categories = ["笔记"]
draft = false
+++

## 环境 {#环境}

接android SDK


## 问题 {#问题}

在SDK返回的时候，部分UI会出现黑块，LOG会提示Opengl GL 502等错误


## 解决思路 {#解决思路}


### 查找根源 {#查找根源}

一开始我的处理方案是把那些异常的资源重新加载，是可以解决大部分问题，但觉得这并不上策。于是我再继续找根源。。。
根源是因为SDK在初始化|登录|支付的回调中去可能去调用到我们的UI，这个回调的线程并不是\*Cocos2dxUI线程\*，这样子操作UI是不能保证先后顺序，这个时候android机子会触发重新加载资源的时机。
所以部分重载一半或者一些不预料的黑块就出现了。

```lua

function ()
    xxx:runAction(cc.Sequence:create(
	cc.DelayTime:create(0.02),
	cc.CallFunc:create(function ()
	    -- ... do something
	end)))
end

```


### 查找为什么会出现黑块 {#查找为什么会出现黑块}


## 结果 {#结果}

完美解决黑块问题