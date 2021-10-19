+++
title = "cocos2dx FileUtils 缓存机制"
author = ["Donghai Ruan"]
date = 2017-05-12T15:36:00+08:00
lastmod = 2021-10-19T09:43:49+08:00
tags = ["cocos2dx", "FileUtils"]
categories = ["笔记"]
draft = false
+++

## 问题描述 {#问题描述}

处理热lua更新功能，有些文件没有没有获取最新下载的资源，依然是使用旧的


## 问题分析 {#问题分析}


### 首先查看搜索路径 {#首先查看搜索路径}

```lua
dump(cc.FileUtils:getInstance():getSearchPaths())
-- 结果是没有问题的
```


### 路径缓存起来 {#路径缓存起来}

```C++
std::string FileUtils::fullPathForFilename(const std::string &filename) const
{
    if (filename.empty())
    {
        return "";
    }

    if (isAbsolutePath(filename))
    {
        return filename;
    }

    // Already Cached ?
    auto cacheIter = _fullPathCache.find(filename);
    if(cacheIter != _fullPathCache.end())
    {
        return cacheIter->second;
    }

    // Get the new file name.
    const std::string newFilename( getNewFilename(filename) );

    std::string fullpath;

    for (const auto& searchIt : _searchPathArray)
    {
        for (const auto& resolutionIt : _searchResolutionsOrderArray)
        {
            fullpath = this->getPathForFilename(newFilename, resolutionIt, searchIt);

            if (!fullpath.empty())
            {
                // Using the filename passed in as key.
                _fullPathCache.insert(std::make_pair(filename, fullpath));
                return fullpath;
            }

        }
    }

    if(isPopupNotify()){
        CCLOG("cocos2d: fullPathForFilename: No file found at %s. Possible missing file.", filename.c_str());
    }

    // The file wasn't found, return empty string.
    return "";
}

```


## 解决方案 {#解决方案}

清空缓存
