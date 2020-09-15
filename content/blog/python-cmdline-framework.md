+++
title = "python 命令行工具的框架"
date = 2019-11-21T16:22:00+08:00
lastmod = 2020-09-15T21:48:05+08:00
tags = ["python"]
categories = ["分享"]
draft = false
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- [框架的结构](#框架的结构)
    - [1.常用的工具 -- utils](#1-dot-常用的工具-utils)
    - [2.命令行的操作解析 -- operation manager](#2-dot-命令行的操作解析-operation-manager)
    - [3.功能插件模块 -- plugin manager](#3-dot-功能插件模块-plugin-manager)
- [github 代码开源](#github-代码开源)

</div>
<!--endtoc-->

在很早前听到过这样一句话“程序中要是反复在写同样一个东西，那就是不合理的”，我们应该尽可能地把能用的部分抽象出来，
比如该有的工具，命令行的解析框架，引用到插件的框架。这些东西都是常用而且在每个项目中是独立的，不会跟其它模块有偶合。
那么正常我们怎么开始写一个命令行工具呢？
<!--more-->


## 框架的结构 {#框架的结构}


### 1.常用的工具 -- utils {#1-dot-常用的工具-utils}


#### crypto {#crypto}

常用加密解密的操作功能的封装


#### fs {#fs}

文件相关操作的二次封装，比如常用的拷贝文件，目录,类似下面的工具类

```python
class FileUtils:
    @staticmethod
    def check_path_in_rules(path, rules):
    @staticmethod
    def copy_files_in_dir(src, dst, rules=None):
    @staticmethod
    def copy_files_in_dir_with_replacement(src, old, new):
    @staticmethod
    def copy_files_in_dir_if_newer(src, dst, cpstat=False):
    @staticmethod
    def remove_files_in_dir(src, ext):
    @staticmethod
    def remove_empty_dirs(dir_path):
```


#### log {#log}

更具个性的输出日志管理

```python
class Log:
    # TODO maybe the right way to do this is to use something like colorama?
    RED = '\033[31m'
    GREEN = '\033[32m'
    YELLOW = '\033[33m'
    MAGENTA = '\033[35m'
    RESET = '\033[0m'
    BLUE = '\033[36m'

    @staticmethod
    def _format(s):
        return s

    @staticmethod
    def _print(s, color=None, format=True):
        if color and sys.stdout.isatty() and sys.platform != 'win32':
            print(color + s + Log.RESET)
        else:
            print(s)

    @staticmethod
    def d(s, format=True):
        Log._print(s, Log.MAGENTA)

    @staticmethod
    def i(s, format=True):
        Log._print(s, Log.GREEN)

    @staticmethod
    def w(s, format=True):
        Log._print(s, Log.YELLOW)

    @staticmethod
    def e(s, format=True):
        Log._print(s, Log.RED)

    @staticmethod
    def t(s, format=True):
        Log._print(s, Log.BLUE)
```


#### other {#other}

其它一些独立的工具，后续添加


### 2.命令行的操作解析 -- operation manager {#2-dot-命令行的操作解析-operation-manager}


#### 让我们的命令行相对合理 {#让我们的命令行相对合理}

命令行对输入的解析，这个问题我思考了许久，命令行我也用的不少，到底怎么样才算是合理，其实这个合理只是满足自已的合理，
我的编程的原则一般从使用层面逆推回来，即先考虑使用的时候我需要哪些参数，例如：
**我们常见的一些命令行**

```shell
git add .
svn ci . -m "comments"
brew install emacs
node install koa2
```

那比如我现在要做一个马甲包命令行工具，我现在叫它"IOSWrapper",那他是不是应该这样

```shell
IOSwrapper obfuscate ~/Documents/projects/demo
IOSWrapper generate ~/Documents/configs/XXXX/config.ini
```

所以我把这种操作封装成一个类---\*Operation\*，然后专门弄一个OperationManager单例来管理这些操作
比如上面提到的obfuscate和generate在我们代码使用中应该是这样的

```python
OperationManager().get_operation('obfuscate').handle_commands(args)
```

然后我们却不需要直接去调用名字，因为这些操作是会从命令行传进来的，我们的代码就变成这样

```python
operation_name = sys.argv[1]
operation = OperationManager().get_operation(operation_name)
if operation:
    operation.handle_commands(sys.argv[2:])
else:
    Log.e("unsupport command.")
```


#### 让一切都动态创建 {#让一切都动态创建}

相信每建一个Operation就要去对应的地方引用一下，这个操作没有人会喜欢，所以让创建的地方动态创建。

```python
class OperationManager(object):
   def get_operation(self, operation_name):
        operation = None

        try:
            operation_full_path = 'operations.%s' % operation_name
            module_t = __import__(operation_full_path)
            opt_module = getattr(module_t, operation_name)
            c = getattr(opt_module, operation_name.title())
            operation = object.__new__(c)
        except Exception as e:
            Log.e(e)
            operation = None

        return operation
```


### 3.功能插件模块 -- plugin manager {#3-dot-功能插件模块-plugin-manager}


#### 功能插件化 {#功能插件化}

这个看看现在的各种工具和编辑器都是具备插件化管理的，为了让同一个功能模块只在一个地方维护，我们应该建一个单独的git版本库来管理它，
例如一个做ICON的小插件，我可能在IOSWrapper和其它工程都会使用到，那么一旦ICON的标准升级，那么我们肯定不愿意每个工程去修改，而是
用一个简单的命令

```shell
git pull
```


#### 动态化创建 {#动态化创建}

这相思想同上


#### 正确地使用姿势 {#正确地使用姿势}

开发N个插件，每个插件用一个git库来管理，需要使用的就git submodule add到工程的plugins


## github 代码开源 {#github-代码开源}

[semgilo/python-frameworks: my python frameworks](https://github.com/semgilo/python-frameworks)
