---
layout: 	  blog
title:		  Debain & Ubuntu 部署生产 Rails
categories: System
tags: 		  Linux Debain Ubuntu Rails Ruby
redirect_from:
  - /web/Rails_Production_for_Debain_and_Ubuntu.html
  - /2015/08/04/Rails_Production_for_Debain_and_Ubuntu/
---


**使用非root账号登陆, 需具备sudo权限.**

# 安装rvm & ruby

```
$ sudo apt-get update
$ sudo apt-get install curl
$ \curl -sSL https://get.rvm.io | bash
```
载入rvm环境: 登出登陆ssh or `$ source ~/.rvm/scripts/rvm`

检查rvm版本: 

```bash
$ rvm -v
```

安装中文支持:

```bash
$ readline: rvm pkg install readline #未使用, 貌似没出啥问题, 估计是版本问题
```
安装ruby, 并指定默认版本: 

```bash
$ rvm use --install --default 2.1.2
$ rvm use --install --default 2.1.2 --with-readline-dir=$rvm_path/usr #未使用
```
若安装readline貌似需要使用:

```bash
$ rvm use 2.1.2@your_gemset --create --default #未使用, 不明白@后需要加什么
```
检查ruby版本: 

```bash
$ ruby -v
```
<!-- more -->
# 安装Passenger
[官方文档](https://www.phusionpassenger.com/documentation/Users%20guide%20Nginx.html#install_on_debian_ubuntu)

导入密钥: 

```bash
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 561F9B9CAC40B2F7
```
安装https传输支持: 

```bash
$ sudo apt-get install apt-transport-https ca-certificates
```

添加对应apt源:

```bash
$ sudo echo " deb https://oss-binaries.phusionpassenger.com/apt/passenger squeeze main" > /etc/apt/sources.list.d/passenger.list
$ sudo apt-get update
```
安装Passenger包: 

```bash
$ sudo apt-get install nginx-extras passenger
```
编辑配置文件`vim nginx.conf`:

```bash
$ sudo vim /etc/nginx/nginx.conf
# passenger_root /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini =>去掉 #
# Passenger_ruby /usr/bin/ruby => Passenger_ruby /home/username/.rvm/wrappers/default/ruby
```
重启nginx: 
```bash
$ sudo service nginx restart
```

# 部署App
创建站点:

```bash
$ sudo /var/www/serverdomain.com
```

安装git: 

```bash
$ sudo apt-get install git
```

克隆项目到服务器: 
 
```bash
/var/www/serverdomain.com $ git clone https://github.com/yourname/yourproject.git
/var/www/serverdomain.com $ mv yourproject current
```
安装数据库, 如`PostgreSQL`:

```bash
$ sudo apt-get install postgresql libpq-dev
```
安装bundle: 

```bash
/var/www/serverdomain.com $ gem install bundler
```
添加gem: 

```bash
/var/www/serverdomain.com $ bundle install
```
生产部署: 

```bash
rake db:migrate RAILS_ENV=production #数据库
rake assets:precompile RAIL_ENV=production #Css
```
 
 