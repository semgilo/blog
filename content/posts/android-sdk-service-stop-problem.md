+++
title = "Android SDK 不会自己删除服务问题"
date = 2017-07-26T16:04:00+08:00
lastmod = 2018-10-31T07:26:55+08:00
tags = ["android", "cocos2dx"]
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

- <span class="section-num">1</span> [环境](#环境)
- <span class="section-num">2</span> [问题](#问题)
- <span class="section-num">3</span> [解决思路](#解决思路)
    - <span class="section-num">3.1</span> [查找根源](#查找根源)
    - <span class="section-num">3.2</span> [解决方案](#解决方案)
- <span class="section-num">4</span> [结果](#结果)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 环境 {#环境}

接android SDK


## <span class="section-num">2</span> 问题 {#问题}

游戏退出或者崩溃后，快速进入游戏会一直崩溃。


## <span class="section-num">3</span> 解决思路 {#解决思路}


### <span class="section-num">3.1</span> 查找根源 {#查找根源}

打开游戏后台发现游戏里面一直有一个服务存在，是SDK的浮窗服务，经过跟渠道交流后，主要有因为SDK
如果浮窗没有初始化成功，就会主动退出游戏。
还有一种情况是服务本身会自起动，所以如果一进游戏未初始化好，服务因为自启动会没有成功导致崩溃


### <span class="section-num">3.2</span> 解决方案 {#解决方案}

修改SDK
在Apllication的onCreate方法里面把浮窗服务先停止并关闭自启动


## <span class="section-num">4</span> 结果 {#结果}

完美解决