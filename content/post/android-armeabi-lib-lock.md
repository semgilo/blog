+++
title = "android-armeabi-lib-lock"
date = 2017-10-13T15:05:00+08:00
lastmod = 2018-10-31T07:26:36+08:00
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

- <span class="section-num">1</span> [问题 cocos2dxlua.so 放在armeabi-v7a下 接SDK又引入 armeabi 几个so 结果会出现找不到cocos2dxlua.so 这个文件 导致崩溃](#问题-cocos2dxlua-dot-so-放在armeabi-v7a下-接sdk又引入-armeabi-几个so-结果会出现找不到cocos2dxlua-dot-so-这个文件-导致崩溃)
- <span class="section-num">2</span> [armeabi 路径会搜索一次 如果找到会锁定 下次就只会在这个目录下查找](#armeabi-路径会搜索一次-如果找到会锁定-下次就只会在这个目录下查找)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 问题 cocos2dxlua.so 放在armeabi-v7a下 接SDK又引入 armeabi 几个so 结果会出现找不到cocos2dxlua.so 这个文件 导致崩溃 {#问题-cocos2dxlua-dot-so-放在armeabi-v7a下-接sdk又引入-armeabi-几个so-结果会出现找不到cocos2dxlua-dot-so-这个文件-导致崩溃}


## <span class="section-num">2</span> armeabi 路径会搜索一次 如果找到会锁定 下次就只会在这个目录下查找 {#armeabi-路径会搜索一次-如果找到会锁定-下次就只会在这个目录下查找}