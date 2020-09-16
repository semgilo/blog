+++
title = "在IOS13.2里面播放视频后调用glReadPixels问题"
date = 2019-11-19T17:10:00+08:00
lastmod = 2020-09-16T11:17:10+08:00
tags = ["cocos2dx"]
categories = ["笔记"]
draft = false
+++

## 问题详情 {#问题详情}

最近因为IOS升级为13.2后，视频无法正常播放，跟往常一样去cocos2dx/github/issues上去找到对应的request合并，
一切都很顺利，但当我使用到截图的时候（调用glReadPixels），程序就崩溃了。
<!--more-->


## 解决过程 {#解决过程}

[glReadPixels crash after VideoPlayer:stop ios 13 · Issue #20270 · cocos2d/cocos2d-x](https://github.com/cocos2d/cocos2d-x/issues/20270)
经过一翻折腾后，只需要把XCode的启动设置修改一下即可
![](/images/glreadpixels-after-videoplayer/file.png)
