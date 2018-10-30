+++
title = "android so path 被锁定"
date = 2018-10-31T01:44:00+08:00
lastmod = 2018-10-31T01:45:26+08:00
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
- <span class="section-num">3</span> [思路及解决](#思路及解决)
    - <span class="section-num">3.1</span> [问题分析](#问题分析)
    - <span class="section-num">3.2</span> [解决方案](#解决方案)
- <span class="section-num">4</span> [结果](#结果)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 环境 {#环境}

接XXSDK的时候


## <span class="section-num">2</span> 问题 {#问题}

java 加载so的时候闪退


## <span class="section-num">3</span> 思路及解决 {#思路及解决}


### <span class="section-num">3.1</span> 问题分析 {#问题分析}

首先定位到闪退的地方是在加载cocos2dxlua.so的地方，因为接过不少SDK，之前SDK在这个地方没有出现闪退的现象，就开始找不同之处。
很快问题就缩小到几个平台的SO文件夹里面，后面经过去一些QQ群讨论，发现linux搜索SO库的时候会有一个“锁定机制”。
也就是第一个去找的路径会被锁定，锁定后就不会再变更，下次再找会直接去这个目录下查找，哪怕是找不到。


### <span class="section-num">3.2</span> 解决方案 {#解决方案}

方法一：将每个平台都放满SO
方法二：没用到的平台直接删除了


## <span class="section-num">4</span> 结果 {#结果}

正常运行。