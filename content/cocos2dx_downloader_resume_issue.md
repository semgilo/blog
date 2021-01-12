+++
title = "cocos2dx downloader 断点续传问题"
author = ["Donghai Ruan"]
date = 2021-01-12T10:28:00+08:00
lastmod = 2021-01-12T10:57:20+08:00
tags = ["cocos2dx", "downloader", "断点续传问题"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

下载过程中突然切换 4G 到 wifi 或者不同 wifi 切换时候，会导致下载后的文件异常


## 问题分析 {#问题分析}


### Android的Downloader是通过requst header来实现断点续传 {#android的downloader是通过requst-header来实现断点续传}

```java
// 在Cocos2dxDownloader里面createTask里面
if (supportResuming && fileLen > 0) {
    // continue download
    List<Header> list = new ArrayList<Header>();
    list.add(new BasicHeader("Range", "bytes=" + fileLen + "-"));
    headers = list.toArray(new Header[list.size()]);
}
```


### 当切换网络的时候依旧是使用旧的header数据，缓存文件也没有还原，导致下载的最终数据比原数据大 {#当切换网络的时候依旧是使用旧的header数据-缓存文件也没有还原-导致下载的最终数据比原数据大}


## 解决方案 {#解决方案}


### 修改重连次数为0 {#修改重连次数为0}

```java
public static Cocos2dxDownloader createDownloader(int id, int timeoutInSeconds, String tempFileNameSufix, int countOfMaxProcessingTasks) {
    Cocos2dxDownloader downloader = new Cocos2dxDownloader();
    downloader._id = id;

    downloader._httpClient.setEnableRedirects(true);
    if (timeoutInSeconds > 0) {
        downloader._httpClient.setMaxRetriesAndTimeout(0, timeoutInSeconds * 1000);
    } else {
        downloader._httpClient.setMaxRetriesAndTimeout(0, 0);
    }

    // downloader._httpClient.setMaxRetriesAndTimeout(3, timeoutInSeconds * 1000);
    downloader._httpClient.allowRetryExceptionClass(javax.net.ssl.SSLException.class);

    //disable url auto decode
    //fix https://github.com/cocos2d/cocos2d-x/issues/18949
    downloader._httpClient.setURLEncodingEnabled(false);

    downloader._tempFileNameSufix = tempFileNameSufix;
    downloader._countOfMaxProcessingTasks = countOfMaxProcessingTasks;
    return downloader;
}
```
