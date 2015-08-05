---
layout: 	  blog
title:		  React 学习笔记 - 初探
categories: Web
tags: 		  React Mac
redirect_from:
  - /web/Study_Note_of_React.html
  - /2015/08/04/Study_Note_of_React/
---

> 公司招安卓两个多月了==依然未果, 于是我来学吧, 老板让我用react这个facetime用的新的开发平台, 貌似iOS, Android, 甚至Rails都通吃, 就是个干!

* DEMO: https://github.com/tokinonagare/AwesomeProject

# 配置

* 官方文档: http://facebook.github.io/react-native/docs/getting-started.html#content

## 注意事项

* _You may need to run brew unlink node if you have previously installed Node._
如果以前安装过node.js的话, 需要 ` brew unlink node`.

* Open AwesomeProject.xcodeproj and hit run in Xcode.
到这步模拟器运行成功, 但是几秒后模拟器出现红屏, 查看terminal: _TypeError: Cannot read property 'root' of null_

解决方法:

```bash
brew upgrade watchman
```

* 参考: https://github.com/facebook/react-native/issues/1875