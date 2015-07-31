---
layout: blog
categories: Mac
title:  彻底删除 LogeMein For Mac
tags: Mac Software
redirect_from:
  - /web/Delete_LogeMein_For_Mac.html
  - /2015/7/30/Delete_LogeMein_For_Mac/
---

前段时间因为需要和老板远程视频, 试验了很多个视频工具, LogMein也是其中的一个, 后面没有使用在Application中删除了, 然而开机后却发现这东西依然会自动启动在menu bar(工具栏)中.

研究了一下, 方法其实很简单

```bash
/library/Application Support/
```

删除里面带有logmein的文件夹即可( 这里我有3个文件夹 ).