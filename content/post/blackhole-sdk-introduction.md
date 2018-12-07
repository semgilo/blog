+++
title = "BlackHole 使用说明"
author = ["semgilo"]
date = 2018-12-07T23:27:00+08:00
lastmod = 2018-12-07T23:55:26+08:00
tags = ["Blackhole"]
categories = ["教程"]
draft = false
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- [配置工程](#配置工程)
    - [网络使用权限](#网络使用权限)
    - [Other Link Flag](#other-link-flag)
- [代码使用](#代码使用)
    - [初始化](#初始化)
    - [启动判断](#启动判断)
    - [生命周期处理](#生命周期处理)
- [测试需求](#测试需求)
    - [7天前](#7天前)
    - [45天前](#45天前)
    - [45天后](#45天后)

</div>
<!--endtoc-->



## 配置工程 {#配置工程}


### 网络使用权限 {#网络使用权限}

```nil
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>NSAllowsArbitraryLoads</key>
	<true/>
</dict>
</plist>
```


### Other Link Flag {#other-link-flag}

```nil
-ObjC
```


## 代码使用 {#代码使用}


### 初始化 {#初始化}

```c
[[BlackHole sharedInstance] onStart:@"20190121230000"];
```


### 启动判断 {#启动判断}

```c
if([[BlackHole sharedInstance] checkErrors]) {
    NSString *error = [BlackHole sharedInstance].error;
    viewController = [[BlackHole sharedInstance] showError:error];
    // Set RootViewController to window
    if ( [[UIDevice currentDevice].systemVersion floatValue] < 6.0)
    {
	// warning: addSubView doesn't work on iOS6
	[window addSubview: viewController.view];
    }
    else
    {
	// use this method on ios6
	[window setRootViewController:viewController];
    }

} else {

    // 游戏原来的启动
}

[[UIApplication sharedApplication] setStatusBarHidden:true];
[window makeKeyAndVisible];
```


### 生命周期处理 {#生命周期处理}

```c
- (void)applicationDidBecomeActive:(UIApplication *)application {
    /*
     Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
     */

    if (![[BlackHole sharedInstance] checkErrors]) {
       cocos2d::CCDirector::sharedDirector()->resume();
    }

}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    /*
     Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
     If your application supports background execution, called instead of applicationWillTerminate: when the user quits.
     */
    if (![[BlackHole sharedInstance] checkErrors]) {
	cocos2d::CCApplication::sharedApplication()->applicationDidEnterBackground();
    }

}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    /*
     Called as part of  transition from the background to the inactive state: here you can undo many of the changes made on entering the background.
     */
    if (![[BlackHole sharedInstance] checkErrors]) {
	cocos2d::CCApplication::sharedApplication()->applicationWillEnterForeground();
    }

}

```


## 测试需求 {#测试需求}


### 7天前 {#7天前}


### 45天前 {#45天前}


### 45天后 {#45天后}