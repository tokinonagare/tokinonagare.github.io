---
layout: 	  blog
title:		  Debain & Ubuntu 基本部署
categories: Linux
tags: 		  Linux Debain Ubuntu ssh
redirect_from:
  - /web/Deploy_for_Debain_and_Ubuntu.html
  - /2015/08/04/Deploy_for_Debain_and_Ubuntu/
---

拿到一台新VPS需要干的几件事情...

# 设置用户
增加用户: 

```bash
adduser username
```

更改密码: 

```bash
passwd  username
```
用户切换: 

```bash
su username
```

## 问题
* _sudo: unable to resolve host._

确定servername: `vim /etc/hostname`

修改host: `vim /etc/hosts`, 在第一行添加:

```
127.0.0.1 localhost localhost.localdomain servername
```
* _username is not in the sudoers file.  This incident will be reported._
切换到root: 

```bash
su root
```
添加edit权限: 

```bash
chmod u+w sudoers
```

添加用户权限: 

```
#User privilege specification": username All=(All) All
```

撤销edit权限: 

```bash
chmod u-w sudoers #一般都懒得管了=.=
```

<!-- more -->
# ssh设置
连接远程主机: 

```bash
ssh username@ip -p 端口 
```
`-p 端口`: 如果不输入默认是`22`.

## 修改ssh端口
`vim /etc/ssh/sshd_config` 去掉 #

```
#Prot 22 
```

增加端口 (新增端口大于8000)

```
Port 22222
```

## 禁止root用户登陆
`vim /etc/ssh/sshd_config`

```
#PermitRootLogin yes => no
```

测试newuser & newprot 可以正常登陆后, 删掉Prot22

## 避免闲置断开

编辑本地`config: vim ~/.ssh/config`

```
Host *
    ServerAliveInterval 120   #每两分钟给服务器发出一个KeepAlive请求 
```
# 设置防火墙