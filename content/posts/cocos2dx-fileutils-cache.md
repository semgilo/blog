+++
title = "cocos2dx FileUtils 缓存机制"
date = 2017-05-12T15:36:00+08:00
lastmod = 2018-10-31T01:58:32+08:00
tags = ["cocos2dx", "FileUtils"]
categories = ["笔记"]
draft = false
+++

<style>
  .ox-hugo-toc ul {
    list-style: none;
  }
</style>
<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">Table of Contents</div>

- <span class="section-num">1</span> [背景](#背景)
- <span class="section-num">2</span> [问题](#问题)
- <span class="section-num">3</span> [思路及解决](#思路及解决)
    - <span class="section-num">3.1</span> [首先查看搜索路径](#首先查看搜索路径)
    - <span class="section-num">3.2</span> [路径缓存起来](#路径缓存起来)
- <span class="section-num">4</span> [结果](#结果)

</div>
<!--endtoc-->



## <span class="section-num">1</span> 背景 {#背景}

处理热lua更新功能


## <span class="section-num">2</span> 问题 {#问题}

有些文件没有没有获取最新下载的资源，依然是使用旧的


## <span class="section-num">3</span> 思路及解决 {#思路及解决}


### <span class="section-num">3.1</span> 首先查看搜索路径 {#首先查看搜索路径}

```lua
dump(cc.FileUtils:getInstance():getSearchPaths())
-- 结果是没有问题的
```


### <span class="section-num">3.2</span> 路径缓存起来 {#路径缓存起来}

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


## <span class="section-num">4</span> 结果 {#结果}

完美解决