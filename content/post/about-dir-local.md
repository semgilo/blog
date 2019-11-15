+++
title = "关于.dir-local文件"
author = ["semgilo"]
date = 2019-11-15T09:51:00+08:00
lastmod = 2019-11-15T11:05:18+08:00
tags = ["emacs", "lisp"]
categories = ["share"]
draft = false
+++

## 关于.dir-local文件 {#关于-dot-dir-local文件}

```nil
Sometimes, you may wish to define the same set of local variables to all the files in a certain directory and its subdirectories, such as the directory tree of a large software project. This can be accomplished with directory-local variables. File local variables override directory local variables, so if some of the files in a directory need specialized settings, you can specify the settings for the majority of the directory's files in directory variables, and then define file local variables in a few files which need the general settings overridden.
```

大概说的意思是我们能在指定目录设置特别变量，文件局部变量会优先目录局部变量。

```lisp
((nil . ((indent-tabs-mode . t)         ;; 设置所有模式下的缩进
         (fill-column . 80)))
 (c-mode . ((c-file-style . "BSD")      ;; 指定c-mode下的file style
            (subdirs . nil)))
 ("src/imported". ((nil . ((change-log-default-name . "ChangeLog.local"))))))  ;; 指定目录下的特殊设置
```


## 如何重新应用dir-locals,当你选择过以后 {#如何重新应用dir-locals-当你选择过以后}

<!--more-->
