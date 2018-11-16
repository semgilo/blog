+++
title = "cocos2dx 重新创建机制"
author = ["semgilo"]
date = 2017-04-30T08:29:00+08:00
lastmod = 2018-11-16T09:46:44+08:00
tags = ["cocos2dx", "android", "EventDispatcher"]
categories = ["笔记"]
draft = false
+++

## 描述 {#描述}

SDK在游戏里面切换activity的时候花屏
<!--more-->


## 分析 {#分析}

会导致自己的shader被删除，截屏丢失，找到重新创建时机，把自己添加的shader重新创建，自己的截图重新截一次


## 细节 {#细节}

cocos2dx 3.x 使用的是EventDispatcher,所有的事件都是向事件分发器注册事件
然后我在CCEventType.h里面找到了

```c++
#define EVENT_COME_TO_FOREGROUND    "event_come_to_foreground"

// The renderer[android:GLSurfaceView.Renderer  WP8:Cocos2dRenderer] was recreated.
// This message is used for reloading resources before renderer is recreated on Android/WP8.
// This message is posted in cocos/platform/android/javaactivity.cpp and cocos\platform\wp8-xaml\cpp\Cocos2dRenderer.cpp.
#define EVENT_RENDERER_RECREATED    "event_renderer_recreated"

// The application will come to background.
// This message is used for doing something before coming to background, such as save RenderTexture.
// This message is posted in cocos/platform/android/jni/Java_org_cocos2dx_lib_Cocos2dxRenderer.cpp and cocos\platform\wp8-xaml\cpp\Cocos2dRenderer.cpp.
#define EVENT_COME_TO_BACKGROUND    "event_come_to_background"
```

那其中\*#EVENT\_RENDERER\_RECREATED\*就是我们想的的事件了，那么在lua具体怎么注册我们的事件呢，以下

```lua
self._backToForegroundListener = cc.EventListenerCustom:create("event_renderer_recreated", function (eventCustom)
    local state = self._skyBox:getGLProgramState()
    local glProgram = state:getGLProgram()
    glProgram:reset()
    glProgram:initWithFilenames("Sprite3DTest/cube_map.vert", "Sprite3DTest/cube_map.frag")
    glProgram:link()
    glProgram:updateUniforms()

end)
cc.Director:getInstance():getEventDispatcher():addEventListenerWithFixedPriority(self._backToForegroundListener, 1)

```


## 结果 {#结果}

解决了恢复位置错乱，及花屏的问题，但加载时间变长了


## 备注 {#备注}

记得在当你不要用的时候，比如节点释放的时候，removeEventListener