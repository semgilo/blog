+++
title = "ios 获取全路径问题"
date = 2018-12-12T12:05:00+08:00
lastmod = 2020-09-16T11:21:44+08:00
tags = ["ios"]
categories = ["笔记"]
draft = false
+++

## 描述 {#描述}

在给游戏资源做加密的时候，发现了一个问题，加密前的对象可以正常获取fullpath,加密后就一直是nil,难道苹果会自己识别
对应资源，如果资源格式被破坏后，就没办法取到fullpath.
   <!--more-->


## 分析 {#分析}

```c
std::string CCFileUtilsIOS::getFullPathForDirectoryAndFilename(const std::string& strDirectory, const std::string& strFilename)
{
    if (strDirectory[0] != '/')
    {
        NSString* fullpath = [[NSBundle mainBundle] pathForResource:[NSString stringWithUTF8String:strFilename.c_str()]
                                                             ofType:nil
                                                        inDirectory:[NSString stringWithUTF8String:strDirectory.c_str()]];
        if (fullpath != nil) {
            return [fullpath UTF8String];
        }
    }
    else
    {
        std::string fullPath = strDirectory+strFilename;
        // Search path is an absolute path.
        if ([s_fileManager fileExistsAtPath:[NSString stringWithUTF8String:fullPath.c_str()]]) {
            return fullPath;
        }
    }
    return "";
}
```

这个是ios 获取资源全路径的方法，因为之前我有在其它项目上成功过，所以这个问题，因为通过对比取不一样的地方，应该就是问题所在。
最后发现，只要是虚拟上目录下就可以正常获取加密的资源，真实目录会检测它对应格式数据是不是正确（我只对png做了测试）。


## 解决 {#解决}

使用虚拟目录，把资源都放虚拟目录就可以正常读取资源了，如果需要多一层目录，可以使用

```c
CCFileUtils::sharedFileUtils()->addSearchPath("res/");
```
