+++
title = "LLVM学习之一搭建"
author = ["semgilo"]
date = 2019-11-26T10:05:00+08:00
lastmod = 2019-11-26T10:29:59+08:00
tags = ["llvm"]
categories = ["学习"]
draft = false
+++

## 探索 {#探索}

先进入官方文档看看
[About — LLVM 10 documentation](https://llvm.org/docs/index.html)
介绍的话我还没看，我现在要先把环境搭起来，所以我来到了下面
![](/images/learn-llvm-setup/1.png)

```nil
The LLVM Getting Started documentation may be out of date. The Clang Getting Started page might have more accurate information.

This is an example workflow and configuration to get and build the LLVM source:

Checkout LLVM (including related subprojects like Clang):

git clone https://github.com/llvm/llvm-project.git
Or, on windows, git clone --config core.autocrlf=false https://github.com/llvm/llvm-project.git
Configure and build LLVM and Clang:.

cd llvm-project

mkdir build

cd build

cmake -G <generator> [options] ../llvm

Some common generators are:

Ninja — for generating Ninja build files. Most llvm developers use Ninja.
Unix Makefiles — for generating make-compatible parallel makefiles.
Visual Studio — for generating Visual Studio projects and solutions.
Xcode — for generating Xcode projects.
Some Common options:

-DLLVM_ENABLE_PROJECTS='...' — semicolon-separated list of the LLVM subprojects you’d like to additionally build. Can include any of: clang, clang-tools-extra, libcxx, libcxxabi, libunwind, lldb, compiler-rt, lld, polly, or debuginfo-tests.

For example, to build LLVM, Clang, libcxx, and libcxxabi, use -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi".

-DCMAKE_INSTALL_PREFIX=directory — Specify for directory the full pathname of where you want the LLVM tools and libraries to be installed (default /usr/local).

-DCMAKE_BUILD_TYPE=type — Valid options for type are Debug, Release, RelWithDebInfo, and MinSizeRel. Default is Debug.

-DLLVM_ENABLE_ASSERTIONS=On — Compile with assertion checks enabled (default is Yes for Debug builds, No for all other build types).

Run your build tool of choice!

The default target (i.e. ninja or make) will build all of LLVM.
The check-all target (i.e. ninja check-all) will run the regression tests to ensure everything is in working order.
CMake will generate build targets for each tool and library, and most LLVM sub-projects generate their own check-<project> target.
Running a serial build will be slow. To improve speed, try running a parallel build. That’s done by default in Ninja; for make, use make -j NNN (NNN is the number of parallel jobs, use e.g. number of CPUs you have.)
For more information see CMake

If you get an “internal compiler error (ICE)” or test failures, see below.

Consult the Getting Started with LLVM section for detailed information on configuring and compiling LLVM. Go to Directory Layout to learn about the layout of the source code tree.

```

因为我用的是mac所以我先后执行的是

```shell
git clone https://github.com/llvm/llvm-project.git
cd llvm-project
mkdir build
cd build
brew install camke
cmake -G Xcode -DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi"  ../llvm
```

一路下来总算顺利，现在可以打开build下的xcode工程，打开是这样的
![](/images/learn-llvm-setup/project.png)
