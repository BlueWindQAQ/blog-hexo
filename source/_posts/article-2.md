---
title: SSL证书让网站从HTTP换成HTTPS
copyright: true
date: 2018-06-15 14:46:46
tags:
	- 阿里云
	- SSL证书
	- HTTPS
categories: 阿里云
keywords: HTTP,HTTPS,SSL,SSL证书,Nginx,阿里云,CSR
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`HTTP` 协议是不加密传输数据的，也就是用户跟你的网站之间传递数据有可能在途中被截获，破解传递的真实内容，所以使用不加密的 `HTTP` 的网站是不太安全的。所以， `Google` 的 `Chrome` 浏览器将在 2017 年 1 月开始，标记使用不加密的 `HTTP` 协议的网站为 `Not Secure`，不安全。

如何改变呢，有免费的吗？
<!--more-->
我的`服务器`和域名都是`阿里云`申请的，所以下面方法基于阿里云操作。
## 证书申请 ##
登录`阿里云`后台，找到，产品与服务-》安全（云盾）-》`SSL`证书（应用安全），找到购买证书
![](https://i.imgur.com/BBN6e5G.png)
进入购买界面后选择"免费型DV SSL"证书，如下图：
![](https://i.imgur.com/4LTM5SS.png)
订单完成后，在订单页面点击“补全”，补全域名（注意现在免费的证书只能使用填写一个域名，且不支持通配符域名配置，因此不支持域名下的二级域名安全认证），然后填写个人信息，按图填写即可：
![](https://i.imgur.com/jTQkwaA.png)
![](https://i.imgur.com/Niz6XKY.png)
![](https://i.imgur.com/McuFsoD.png)
申请审核通过后会收到邮件，意思是云盾证书开通成功
## 配置SSL证书 ##
`SSL`证书审核通过后在之前的订单页面就可以看到证书下载入口
![](https://i.imgur.com/7XtyGA7.png)
进入下载页面，点击下载按钮“下载证书for Nginx”，下载证书，然后按照提示操作即可
![](https://i.imgur.com/QPE7nSX.png)
以下是阿里提供的方法：
安装证书
文件说明：
1.证书文件214776764040878.pem，包含两段内容，请不要删除任何一段内容。
2.如果是证书系统创建的CSR，还包含：证书私钥文件214776764040878.key。
( 1 ) 在`Nginx`的安装目录下创建`cert`目录，并且将下载的全部文件拷贝到`cert`目录中。如果申请证书时是自己创建的CSR文件，请将对应的私钥文件放到`cert`目录下并且命名为214776764040878.key；
( 2 ) 打开 `Nginx` 安装目录下 `conf` 目录中的 `nginx.conf` 文件，找到：
```
# HTTPS server
# #server {
# listen 443;
# server_name localhost;
# ssl on;
# ssl_certificate cert.pem;
# ssl_certificate_key cert.key;
# ssl_session_timeout 5m;
# ssl_protocols SSLv2 SSLv3 TLSv1;
# ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
# ssl_prefer_server_ciphers on;
# location / {
#
#
#}
#}
```
( 3 ) 将其修改为 (以下属性中`ssl`开头的属性与证书配置有直接关系，其它属性请结合自己的实际情况复制或调整) :
```
	server {
    listen 443;
    server_name localhost;
    ssl on;
    root html;
    index index.html index.htm;
    ssl_certificate   /usr/local/nginx/cert/214776764040878.pem;
    ssl_certificate_key  /usr/local/nginx/cert/214776764040878.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    location / {
        root html;
        index index.html index.htm;
    }
	}
```
保存退出。
( 4 )重启 `Nginx`。
( 5 ) 通过 https 方式访问您的站点，测试站点证书的安装配置。
3.配置`443端口`
`SSL`使用的`443端口`，需要服务器开放`443端口`
方法：在`阿里云`服务器控制台添加安全组，配置`443端口`监听即可
![](https://i.imgur.com/hfIIV1L.png)
配置后即可通过https访问网站，并且浏览器认证安全
![](https://i.imgur.com/888WcC9.png)
参考文档：https://ninghao.net/blog/4449
