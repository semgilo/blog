+++
title = "mac下svn常用操作命令"
date = 2018-11-16T14:33:00+08:00
lastmod = 2020-09-16T11:13:17+08:00
tags = ["SVN"]
categories = ["分享"]
draft = false
+++

## svn配置BeyondCompare对比工具 {#svn配置beyondcompare对比工具}

[Beyond Compare 官方说明](https://www.scootersoftware.com/features.php?zz=kb%5Fvcs%5Fosx)


## 常规操作 {#常规操作}


### 检出工程 {#检出工程}

```shell
svn co url
```


### 查看当前状态 {#查看当前状态}

```shell
svn st
```


### 添加文件或者文件夹 {#添加文件或者文件夹}

```shell
svn add . --force
```


### 查看当前修改 {#查看当前修改}

```shell
svn diff
```


### 其它 {#其它}

svn help


## 进阶操作 {#进阶操作}


### 删除所有不存在的文件和文件夹 {#删除所有不存在的文件和文件夹}

```shell
svn st|awk '{if($1=="!"){print $2}}'|xargs svn rm
```


### 增加？号文件 {#增加-号文件}

```shell
svn st|awk '{if($1=="?"){print $2}}'|xargs svn add
```


### 提交m文件文件 {#提交m文件文件}

```shell
svn st|awk '{if($1=="m"){print $2}}'|xargs svn ci -m "comments"
```

```shell
svn st|awk '{if($1=="m" and index($2,"@")==0){printf "%s@",$2}}'|xargs svn ci -m "comments"
```
