+++
title = "Unity快速入门"
author = ["Donghai Ruan"]
date = 2021-06-19T15:35:00+08:00
lastmod = 2021-10-19T09:44:05+08:00
tags = ["Unity"]
categories = ["笔记"]
draft = false
+++

## C#特色语法(部分是 Unity 支持的) {#c-特色语法--部分是-unity-支持的}


### 特别两个关键字 {#特别两个关键字}


#### ref {#ref}

-   引用传递
-   跟 C++的引用传递是一样的


#### out {#out}

-   从字义理解它是想把值传到外面来
-   是引用参数


#### 差异 {#差异}

-   用途上不一样，在一定意义上 ref 是可以替代 out
-   值初始化方式，out 的值在外部并不处理，只是提供地址


### 特色点 {#特色点}


#### Delegate {#delegate}

-   直接代理方法
-   可以直接合并多个操作
-   直接+-操作


#### Coroutine {#coroutine}

-   启动后根据返来的 IEnumerlator 来选择 Resume
-   跳出协程 yield break;
-   下一帧 yield return null;
-   延迟一段时间 yield return new Delay
-   根据指定条件 yield return new WaitUtil(() => {return condition;});
-   可以嵌套其它 IEnumerator


#### Atribute p {#atribute-p}

-   有点像宏定义
-   提前声明一些结编译器知道的内容，会比宏看起来更直观一些


#### Indexer {#indexer}

-   把索引进一步的抽象出来


#### Property {#property}

-   跟 objc 的有点像，但写法不一样


## Unity {#unity}


### 界面的处理 {#界面的处理}


#### 脚本的挂载方式，直接通过 C#（LuaBehaviour） {#脚本的挂载方式-直接通过-c-luabehaviour}


#### 直接在 Lua 里面搞 {#直接在-lua-里面搞}


### 几个路径说明 {#几个路径说明}

| 路径                            | 可读写 | 说明       | Editor          | android     | ios              |
|-------------------------------|-----|----------|-----------------|-------------|------------------|
| Application.dataPath            | 只读 | 相当是游戏根目录 | Assets          | apk 目录    | xxx.app/Data     |
| Application.streamingAssetsPath | 只读 | 存放一些二进制文件 | StreamingAssets | assets      | xxx.app/Data/Raw |
| Application.persistentDataPath  | 可读写 | 本地存储，即下载位置 | 运行时生成      | data/files/ | Documents        |
| Application.temporaryCachePath  |     | 临时数据缓存目录 |                 | data/cache  | Library/Caches   |


### Camera {#camera}


#### 透视模式 {#透视模式}

转化屏幕坐标时候得注意 z 轴的距离


### 层级管理 {#层级管理}


#### Sort Layer {#sort-layer}

可以定义自己 Layer 是第一级排序，在 Layer 里面还可以进行二级排序


### Pixels Per Unit {#pixels-per-unit}


#### 资源导入时候设置 {#资源导入时候设置}

<!--list-separator-->

-  UI 一般默认用 100

<!--list-separator-->

-  TileMap 则根据块大小，一般是设置为与块大小一致比如（64\*64）设置为 64


## xLua {#xlua}
