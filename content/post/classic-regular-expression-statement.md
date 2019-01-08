+++
title = "经典正则表达式语句"
author = ["semgilo"]
date = 2018-12-24T18:10:00+08:00
lastmod = 2019-01-05T18:08:04+08:00
tags = ["regex"]
categories = ["笔记"]
draft = false
+++

## 推荐学习地方 {#推荐学习地方}

[http://www.zjmainstay.cn/](http://www.zjmainstay.cn/)


## 会持续更新中 {#会持续更新中}

python 需要

```python
pip install regex
```

```python
VAR_TYPE = r'id|void|int|bool|BOOL|float|double|\bCG[\w]+\b|\bUI[\w]+\b|\bNS[\w]+\b|[\w]+[ ]*\*[ ]*'
```


### 双引号里面的内容 {#双引号里面的内容}

```python
r'\"(?:[^"\\]|\\.)*\"'
```


### 注释 (_****\*****_) {#注释}

```python
r'/[*][\s\S]+?[*]/'
```


### 选取方法块(objc) {#选取方法块--objc}

```python
r'[ ]*[+-][ ]*\((%s)\)[ ]*(\w+)[ ]*(:.*)?[ ]*[\s]+?(?<rec>\{(?:[^{}]+|(?&rec))*\})' % VAR_TYPE
```