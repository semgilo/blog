+++
title = "当CCScrollView在RenderTexture可能裁剪框适配有问题"
author = ["Donghai Ruan"]
date = 2021-04-30T15:20:00+08:00
lastmod = 2021-10-19T10:34:27+08:00
tags = ["cocos2dx", "RenderTexture", "ScrollView", "适配"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

当 CCScrollView 在 RenderTexture 可能裁剪框适配有问题
<!--more-->


## 问题分析 {#问题分析}


### 有问题的是 CCScrollView，聊天出问题的是 CCTableView（其基类是 CCScrollView) {#有问题的是-ccscrollview-聊天出问题的是-cctableview-其基类是-ccscrollview}


### 有问题的情况，当 RenderTexture 里面的 viewport 与 GLView 的 viewport 不一致的时候 {#有问题的情况-当-rendertexture-里面的-viewport-与-glview-的-viewport-不一致的时候}


### CCScrollView 使用的是 glScissor 方式进行裁剪，主要影响方法 CCScrollView::getViewRect() {#ccscrollview-使用的是-glscissor-方式进行裁剪-主要影响方法-ccscrollview-getviewrect}


## 解决方案 {#解决方案}


### 在 RenderTexture 里面添加获取当前绘制的实例对象接口（CCRenderTexture::getRenderingInstatnce()） {#在-rendertexture-里面添加获取当前绘制的实例对象接口-ccrendertexture-getrenderinginstatnce}


### 在 CCScrollView 里面添加对 RenderTexture 的适配 {#在-ccscrollview-里面添加对-rendertexture-的适配}

```C++
auto rt = RenderTexture::getRenderingInstance();
if (rt != nullptr) {
    auto frameSize = Director::getInstance()->getOpenGLView()->getFrameSize();
    auto viewport = rt->getVirtualViewport();
    screenPos.x = screenPos.x * viewport.size.width / frameSize.width + viewport.origin.x;
    screenPos.y = screenPos.y * viewport.size.height / frameSize.height + viewport.origin.y;
    scaleX *= viewport.size.width / frameSize.width;
    scaleY *= viewport.size.height / frameSize.height;
}
```
