+++
title = "android 分屏适配"
author = ["Donghai Ruan"]
date = 2021-01-09T14:32:00+08:00
lastmod = 2021-06-16T10:18:09+08:00
tags = ["android", "split-screen", "分屏", "适配"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

由于 android 可以开启分屏和华为的折叠屏导致游戏适配问题


## 问题分析 {#问题分析}


### [官方文档](https://developer.android.com/guide/topics/ui/multi-window) {#官方文档}


#### android 7.0 开始支持 multi-windows {#android-7-dot-0-开始支持-multi-windows}


#### android 8.0 支持画中画 {#android-8-dot-0-支持画中画}


#### 多窗口模式不会更改 Activity 生命周期 {#多窗口模式不会更改-activity-生命周期}


#### 适配标识 android:resizeableActivity {#适配标识-android-resizeableactivity}


#### 画中画支持 android:supportsPictureInPicture {#画中画支持-android-supportspictureinpicture}


#### 配置更改 android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation" {#配置更改-android-configchanges-screensize-smallestscreensize-screenlayout-orientation}


#### 判断方法 {#判断方法}

```java
// Activity 提供以下方法来支持多窗口显示。
isInMultiWindowMode()
// 调用该方法可确认 Activity 是否处于多窗口模式。
isInPictureInPictureMode()
```


#### 生命周期 {#生命周期}

```java
onMultiWindowModeChanged()
onPictureInPictureModeChanged()
```


### Cocos2d-x 的 GlView 创建流程 {#cocos2d-x-的-glview-创建流程}

Cocos2dxActivity -> Cocos2dxGLSurfaceView -> Cocos2dxRender -> GLView


### 当屏幕尺寸发生改变的 {#当屏幕尺寸发生改变的}

Cocos2dxGLSurfaceView -> Cocos2dxRender -> GLView


### 需要处理的问题 {#需要处理的问题}


#### 当窗口大小发生改变的时候不重新创建 OpenGLView {#当窗口大小发生改变的时候不重新创建-openglview}


#### 窗口大小改变的事件传递 java -> c++ -> lua {#窗口大小改变的事件传递-java-c-plus-plus-lua}


#### 窗口大小改变后点击事件位置适配问题 {#窗口大小改变后点击事件位置适配问题}


#### 窗口大小改变后游戏界面的适配 {#窗口大小改变后游戏界面的适配}


## 解决方案 {#解决方案}


### 在 AndroidManifest.xml 添加下列参数，让 OpenGLView 不因为调整大小而重建 {#在-androidmanifest-dot-xml-添加下列参数-让-openglview-不因为调整大小而重建}

```xml
// android:configChanges
"smallestScreenSize|density|screenLayout"
```


### 传递尺寸更改信息 {#传递尺寸更改信息}

```c++
// cocos/client/platform/android/javaactivity-android.cpp
void Java_org_cocos2dx_lib_Cocos2dxRenderer_nativeOnSurfaceChanged(JNIEnv*  env, jobject thiz, jint w, jint h)
{
    cocos2d::Application::getInstance()->applicationScreenSizeChanged(w, h);
}
```

-   [X] 传递给 lua

<!--listend-->

```c++
void AppDelegate::applicationScreenSizeChanged(int newWidth, int newHeight)
{
    // ...
    auto engine = LuaEngine::getInstance();
    LuaStack* luaStack = engine->getLuaStack();
    luaStack->pushInt(newWidth);
    luaStack->pushInt(newHeight);
    luaStack->executeGlobalFunction("applicationScreenSizeChanged", 2);
    luaStack->clean();
}
```

```lua
function applicationScreenSizeChanged(width, height)
    update("game/ui/base/AlignM");
    EventMgr.fire(event.GLVIEW_SIZE_CHANGED, {width = width, height = height});
    local forms = CS():getFormList();
    for i, v in ipairs(forms) do
        if v.resize then
            trace("NativeInterface", string.format("%s: resize", v:getName()))
            v:resize()
        end
    end
end
```


### 当屏幕大小改变后需要对 Director 里面数据进行更新 {#当屏幕大小改变后需要对-director-里面数据进行更新}

```c++
// CCDirecotr 添加onGLViewSizeChanged(int width, int height)
void Director::onScreenSizeChanged(int width, int height)
{
    // 更新窗口大小
    _winSizeInPoints.width = width;
    _winSizeInPoints.height = height;

    // 重建状态标签及位置
    createStatsLabel();

    // 重新设置GL相关值
    setGLDefaultValues();
}
```


### 当屏幕尺寸大小发生改变后，lua 界面相关适配 <code>[0%]</code> {#当屏幕尺寸大小发生改变后-lua-界面相关适配}


#### 抛出屏幕尺寸更改事件(GLVIEW\_SIZE\_CHANGED) {#抛出屏幕尺寸更改事件--glview-size-changed}


#### 截图没有及时更新 {#截图没有及时更新}


#### 使用 setShifting 导致屏幕切换后要重新设置 {#使用-setshifting-导致屏幕切换后要重新设置}


#### 使用 initScrollGridsVertical 之后，ScrollView 大小发生改变，没有更新 {#使用-initscrollgridsvertical-之后-scrollview-大小发生改变-没有更新}


#### 界面走光问题,特效或者图片需要增加尺寸 {#界面走光问题-特效或者图片需要增加尺寸}


#### 更新过程中没有收到切换事件 {#更新过程中没有收到切换事件}

UOB->wizcall("CF():redrawGrids(true)")

UOB->wizcall("local node = CF().node CF().scrollView = findChildByName(node, \\"CT/scroll\_view\\")")

UOB->wizcall("return CF().scrollView:getPositionX()")
UOB->wizcall("CF().pv = findChildByName(CF().node, \\"CT/page\_view\\")")

find\_object\_by\_rid("FEGH4E4000H670")->set("door\_opened", 1);
G\_BROADCAST\_INVOKE(GROUP\_D, "notify\_updated", find\_object\_by\_rid("FEGH4E4000H670")->get\_detail());
