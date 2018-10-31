+++
title = "cocos2dx etc 闪退问题"
date = 2017-05-16T10:19:00+08:00
lastmod = 2018-10-31T07:28:58+08:00
tags = ["cocos2dx", "etc1", "android"]
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
- <span class="section-num">3</span> [思路及解决](#思路及解决)
    - <span class="section-num">3.1</span> [问题分析](#问题分析)
    - <span class="section-num">3.2</span> [解决方案](#解决方案)
- <span class="section-num">4</span> [结果](#结果)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 环境 {#环境}

稳定性测试期


## <span class="section-num">2</span> 问题 {#问题}

部分机型和部分模拟器会因为ETC1资源导致闪退

```java
07-13 21:59:43.727 E/szipinf (21935): Error reading asset data
07-13 21:59:43.727 E/szipinf (21935): Unable to access asset data: -1
```


## <span class="section-num">3</span> 思路及解决 {#思路及解决}


### <span class="section-num">3.1</span> 问题分析 {#问题分析}

经过调试发现是因为只有部分ETC1资源没有办法正常加载，于是我就把没办法正常加载的资源取出来找规律，
然后我发现了它们都有一个相同的问题，就是有大部分的透明区域，就是没有用的区域，大概是因为ETC1格式压缩
的问题和这些闪退的机型协议不一致所导致的，我也不想去深究。


### <span class="section-num">3.2</span> 解决方案 {#解决方案}

我尝试在整张图片周围画一个距形的红框，目的让资源四周都有像素，果然闪退解决


## <span class="section-num">4</span> 结果 {#结果}

做图工具升级，完美解决