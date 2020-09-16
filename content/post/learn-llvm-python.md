+++
title = "使用clang的bindings之python"
date = 2019-11-26T10:30:00+08:00
lastmod = 2020-09-16T11:22:16+08:00
tags = ["llvm", "python"]
categories = ["learn"]
draft = true
+++

这次主要的目的是要使用clang来解析c、c++和objc，然后提取出类名，方法，成员，然后精准混淆。之前使用的是grep来提取和混淆，
虽然想尽一切办法，还是有许多漏洞，实在无心修补了。
