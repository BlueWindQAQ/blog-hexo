---
title: linux 配置Nginx
copyright: true
date: 2018-06-11 18:22:25
tags:
	- linux
	- nginx
categories: linux
---
Nginx 是 C语言 开发，建议在 Linux 上运行，当然，也可以安装 Windows 版本，本篇则使用 CentOS 7 作为安装环境。

首先下载资源包：
　安装Nginx之前，首先要安装好编译环境gcc和g++，然后以CentOS为例安装Nginx，安装Nginx需要PRCE库、zlib库和ssl的支持，除了ssl外其他的我们都是去官网下载：
　　Nginx：http://nginx.org/
　　PCRE：http://www.pcre.org/
　　zlib：http://www.zlib.net/
<!--more-->
# 安装所需环境 #

## 一. gcc 安装 ##
安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：<br>
> yum install gcc-c++
## 二. PCRE pcre-devel 安装 ##
PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：<br>
> yum install -y pcre pcre-devel
## 三. zlib 安装 ##
zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。
> yum install -y zlib zlib-devel
## 四. OpenSSL 安装 ##
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。<br>
nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。<br>
> yum install -y openssl openssl-devel

安装Nginx：
解压Nginx压缩包tar -xvzf nginx-1.9.8.tar.gz，进入解压后文件夹
配置：./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module
 编译
```
    # make
    
     安装
    #  make install
```
 检查是否安装成功
```
    cd  /usr/local/nginx/sbin ./nginx -t
    结果显示：
    nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
    nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```
配置防火墙80端口
```
	#修改防火墙配置： 
    # vi + /etc/sysconfig/iptables
    #添加配置项 
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
    #重启防火墙 
    # service iptables restart
```
 启动停止重启与测试
1.启动

```
    #方法1
    # /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
    #方法2
    # cd /usr/local/nginx/sbin
    # ./nginx
```
2.停止
       
```
    #查询nginx主进程号 
    ps -ef | grep nginx
    #停止进程 
    kill -QUIT 主进程号 
    #快速停止 
    kill -TERM 主进程号 
    #强制停止 
    pkill -9 nginx
```
3.重启(首次启动需：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf)<br>

```
/usr/local/nginx/sbin/nginx -s reload
```

    
4.测试

```
#测试端口 
netstat -na | grep 80
```
![](https://i.imgur.com/22nffVv.png)
	
![](https://i.imgur.com/acLRlHa.png)