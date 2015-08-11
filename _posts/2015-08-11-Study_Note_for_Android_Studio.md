---
layout: 	  blog
title:		  Andriod Studio 学习笔记 - 初探
subtitle:   安装 基本设置
categories: App
tags: 		  Andriod Java Mac Andriod_Studio 
redirect_from:
  - /web/Study_Note_for_Android_Studio.html
  - /2015/08/11/Study_Note_for_Android_Studio/
---

> 接触几天React后发现Andriod的开发版本还要等几个月, 于是转战Android_Studio.

## Demo地址: https://github.com/tokinonagare/AndroidStudioDemo

# 安装

## 跳过启动检测认证

```
Android Studio.app/Contents/bin/idea.properties
```
尾行添加

```
disable.android.first.run=true
```

## 安装SDK
翻墙必须, 使用Shadowsocks的情况下, 修改preference中的HTTP无效, 使用全局模式

```
主界面 > configure > SDKmanager
```

_注意: 改变网络环境情况下需要 `Tool > reload`_

# 设置更改

## 快捷键: preference > keymap

根据自己subl的编码习惯改掉如下快捷键

* Move care to Next Word : 光标移动到下一个字符 > Shift + Space
* Split Line : 回车, 光标不移动 > Shift + Enter
* Start New line : 新建一行, 光标移动到新建行 > Command + Enter
* Start New line Before Curre : 在该行上一行新建一行,光标移动到新建行 > Command + Shift + Space

未修改

* 查看详情 Control + J
* 快速修复 Alt + Enter


## 主题更改&字体更改

```
Settings > Appearance
```
我选择了一个Darcula的黑色主题, 不太喜欢白色的背景, 感觉很刺眼.
中文显示问题的话, 字体可以考虑使用 Microsoft Sans Serif(微软雅黑).



# 其他

真机连接调试的话需先在手机上开启`usb调试模式`再插入usb.
