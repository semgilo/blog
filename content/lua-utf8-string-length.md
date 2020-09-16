+++
title = "如何在lua正确识别字符串长度"
date = 2017-04-28T15:06:00+08:00
lastmod = 2020-09-16T15:15:54+08:00
tags = ["lua"]
categories = ["笔记"]
draft = false
+++

```lua
function getCharacterLen(str)
    local nStrLen = 0
    for uchar in string.gfind(replaceNewLine(str), "[%z\1-\127\194-\244][\128-\191]*") do
        if string.len(uchar) == 3 then
            nStrLen = nStrLen + 2
        else
            nStrLen = nStrLen + 1
        end
    end
    return nStrLen
end
```
