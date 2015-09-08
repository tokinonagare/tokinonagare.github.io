---
layout: 	  blog
title:		  Andriod Studio SDK & .Gitignore 问题处理
categories: Andriod
tags: 		  Mac Andriod_Studio git
redirect_from:
  - /web/SDK_and_Gitignore_Android_Studio.html
  - /2015/08/27/SDK_and_Gitignore_Android_Studio/
---

> 接触两周Android Studio 记录在处理SDK下载问题和.Gitignore如何配置的方法

# SDK 
问题主要是出在` http://dl-ssl.google.com/`这个`Domian`的Fetching上

_Failed to fetch URL http://dl-ssl.google.com/glass/gdk/addon.xml, reason: HttpHostConnect_

当然如果直接用代理是可以下载, 但是速度感人.

## 方法

__使用代理跑一遍Fetching, 关掉SDK Manager, 再次启动SDK Manager, SDK 下载速度就正常了__

猜测是因为Fetching一次之后, 会有一个缓存, 再不重启Android Studio的前提下, SDK Manager会直接读缓存

# .Gitignore

```bash
# Built application files
build/

# Local configuration file (sdk path, etc)
local.properties

# Gradle generated files
.gradle/

# User-specific configurations
.idea/**/*.xml
*.iml

```

Mac 下面的话这几个`ignore`项目的话就足够了

```
.idea/**/*.xml
```

意思是在.idea文件夹下的任意目录下带`.xml`后缀的文件.

```bash
git rm -r . --cached
```

允许递归`-r`的方法清除该文件夹下的所有缓存索引.

```bash
git add .
git commit -m "Change .gitignore file"
```

* 看到有文章将`gradle.xml`文件例如非ignore的文件

```
!.idea/gradle.xml
```

不过仔细研究之后发现这个文件只在win系统下的opnefile的对话框中会影响文件夹的图标.