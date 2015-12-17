---
layout: 	  blog
title:		  ssh登入当前vpn服务器
subtitle:     学习笔记
categories:   linux
tags: 		  router interface
redirect_from:
  - /web/ssh_access_current_vpn_server.html
  - /2015/12/17/ssh_access_current_vpn_server/
---

最近应老板要求把公司的所有的网络包都转发到了一台vps的vpn服务器（本地使用树莓派进行的一个实现，准备稳定之后进行一个记录）。然而再如何用ssh登入这台vpn服务器上却遇到了一点麻烦。

# 问题简述

```bash
ssh root@xxx.xxx.xxx.xxx
```
通常使用这种登陆方式的话，会因为已经是在一个内网的状况下将导致无法连接。

# 解决方法
## 直连
首先想到的办法是：重新做一个接口和无线wifi，不通过vpn进行以往的正常访问。

## 更改router or iptables
既然我是想要ssh能够正常登入server，那么只要让ssh的22端口不通过vpn即可。

```bash
-A fw-interfaces -s 192.168.8.0/24 -i eth0 -o tun+ -j ACCEPT
```

## 访问当前内网ip
在方法2的寻找解决办法中，突然自问：为什么我现在在vpn的网络环境下依然可以访问192.168.8.2（raspberry）？好像有了灵感。

```bash
ifconfig

tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          inet addr:10.8.0.6  P-t-P:10.8.0.5  Mask:255.255.255.255
```
vpn的转发无非是通过vps server作为一个出口的路由来实现的，那么我是否可以通过内网的ip登入？

```bash
ping 10.8.0.6
# 当前raspberry tun0 在vps server 下的ip

bingo! 

ping 10.8.0.1

bingo!
```
不用进行任何修改，完美解决问题，如何透彻的理解事物的运作原理才是解决问题的最佳方案！

```
ssh root@10.8.0.1
```

