+++
title = "经典正则表达式语句"
author = ["Donghai Ruan"]
date = 2018-12-24T18:10:00+08:00
lastmod = 2021-10-19T10:34:13+08:00
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


### 命名修改 {#命名修改}

```lua
命名规则

-- 替换可能为关键字的对象为指定字符串
\b(?:n|t|p|str|obj|f|b|e)End\b
ended

-- 替换命名规则
(?<!cc.)\b(?:n|t|p|str|e|obj|f|b)([A-Z]+)
\L$1

-- 替换配置方法ch_xxx
ch_(\w+)(\(.*\))
cfg["formula"].$1$2

-- 替换读表方式

-- 替换类结构
C([A-Z]\w+) = (class\("\1"[\S\s]*)
local C$1 = $2\nreturn C$1

-- 替换类的使用
(?<!function[ ])(?<!function[ ][ ])C([A-Z]\w+)(?:\:create|\.new)(\(.*\))
core.battle.$1$2$3

-- 替换以 C 开头的命令方式
(?<!\.)\bC([A-Z](?![A-Z]{3,}\b)[A-Za-z0-9]+)\b(?![ ]*=[ ]\d+)

-- 删除 core.的命令方式
(?<!\.)(?<!\")core\.

-- 替表格命名格式
([cC]onfig\.)([a-z]+)([A-Z][a-z]+)
```
