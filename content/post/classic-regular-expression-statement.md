+++
title = "经典正则表达式语句"
author = ["semgilo"]
date = 2018-12-24T18:10:00+08:00
lastmod = 2018-12-26T23:52:44+08:00
tags = ["regex"]
categories = ["笔记"]
draft = false
+++

## 会持续更新中 {#会持续更新中}

```python

VAR_TYPE = r'id|void|int|bool|BOOL|float|double|\bCG[\w]+\b|\bUI[\w]+\b|\bNS[\w]+\b|[\w]+[ ]*\*[ ]*'

```


### 双引号里面的内容 {#双引号里面的内容}

```python
r'\"(?:[^"\\]|\\.)*\"'
```


### 注释 (_****\*****_) {#注释}

```python
r'/[*]([^*]|([*][^/]))*[*]/'
```


### 选取方法块 {#选取方法块}

```python
r'[ ]*[+-][ ]*\((%s)\)[ ]*(\w+)[ ]*(:.*)?[ ]*[\s]+?(?<rec>\{(?:[^{}]+|(?&rec))*\})' % VAR_TYPE
```