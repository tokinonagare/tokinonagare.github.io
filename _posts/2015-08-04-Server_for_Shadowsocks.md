---
layout: 	  blog
title:		  Debain & Ubuntu 部署 Shadowsocks
categories: System
tags: 		  Linux Debain Ubuntu Shadowsocks 
redirect_from:
  - /web/Server_for_Shadowsocks.html
  - /2015/08/04/Server_for_Shadowsocks/
---

# Server 部署:

```bash
apt-get install update
```

* 参考: http://www.chedanji.com/ubuntu-shadowsocks/

## 处理问题
_Error: Another program is already listening on a port that one of our HTTP servers is configured to use. Shut this program down first before starting supervisord._

```bash 
sudo unlink /tmp/supervisor.sock 
```
&

```bash
sudo unlink /var/run/supervisor.sock
```
均出现错误: 
_unlink: cannot unlink `/var/run//supervisor.sock': No such file or directory_

`vim /etc/supervisord.conf`发现
`file=/var/run//supervisor.sock   ; (the path to the socket file) `
这行多了一个`"/"`去掉搞定.**(注: 这个应该是作为一个隐藏文件的处理, 但不知道为何没有处理好)**

```bash
service supervisor start
```

* 参考: http://stackoverflow.com/questions/14479894/stopping-supervisord-shut-down 

<!-- more -->

# Web 控制部署:

`vim /etc/supervisor/conf.d/shadowsocks.conf`

```
[inet_http_server] 
port = 127.0.0.1:9001 
username = user 
password = 123
```

```bash
supervisorctl reload
```