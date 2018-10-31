+++
title = "cocos2dx 重新创建机制"
date = 2017-04-30T08:29:00+08:00
lastmod = 2018-10-31T07:29:32+08:00
tags = ["cococs2dx", "android", "EventDispatcher", "recreate"]
categories = ["笔记"]
draft = false
+++

<style>
  .ox-hugo-toc ul {
    list-style: none;
  }
</style>
<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- <span class="section-num">1</span> [背景](#背景)
- <span class="section-num">2</span> [问题](#问题)
- <span class="section-num">3</span> [解决思路](#解决思路)
    - <span class="section-num">3.1</span> [重新创建时机](#重新创建时机)
- <span class="section-num">4</span> [结果](#结果)
- <span class="section-num">5</span> [注意事项](#注意事项)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 背景 {#背景}

接android SDK的时候


## <span class="section-num">2</span> 问题 {#问题}

SDK在游戏里面切换activity的时候，会导致自己的shader被删除，截屏丢失


## <span class="section-num">3</span> 解决思路 {#解决思路}

找到重新创建时机，把自己添加的shader重新创建，自己的截图重新截一次


### <span class="section-num">3.1</span> 重新创建时机 {#重新创建时机}

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


## <span class="section-num">4</span> 结果 {#结果}

解决了恢复位置错乱，及花屏的问题，但加载时间变长了


## <span class="section-num">5</span> 注意事项 {#注意事项}

记得在当你不要用的时候，比如节点释放的时候，removeEventListener