+++
title = "巧妙的使用coroutine分散计算量"
author = ["Donghai Ruan"]
date = 2017-04-28T15:06:00+08:00
lastmod = 2021-10-19T10:34:33+08:00
tags = ["lua"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

最近一直在优化android相关的内容
在战斗模块上吃了一个亏，本应该可以忽略的一部分，寻敌跟寻位耗大量的CPU时间导致战斗暴卡


## 问题分析 {#问题分析}


### 优化算法 {#优化算法}

一开始是尽量优化整个算法，但游戏本身在寻敌和寻位的复杂度就要有一定的开销，降低了差不多有一半的消耗，但最终还是达不到一个理想的效果


### 多线程 {#多线程}

考虑到现在android单核计算能力实在有限，就开始考虑用多线程来单独计算战斗模块。
因为战斗是用纯lua写的，如果要用单独纯种就得多开一个luaState，而且数据交互也是比较麻烦，因时间关系就先放下了。


### 协程 {#协程}

在lua里面还有一个神器就是协程了，因为战斗模型的一帧是100ms，游戏一界面表现一帧是16ms，所以过程就提供了分担的时间。
方式一

```lua
for i=1,10 do
    -- do something
    -- ...
    if i % 2 == 0 then
        coroutine.yield()
    end
end
```

方式二

```lua
local nLastClock = os.clock()
for i=1,10 do
    -- do something
    -- ...
    if os.clock() - nLastClock > 16 then
        coroutine.yield()
    end
end
```


## 解决方案 {#解决方案}

使用协程的方案，一旦在coroutine.yield()前的数据是生效的，切记在表现的时候要把这部分的未完全执行完的帧resume起来
