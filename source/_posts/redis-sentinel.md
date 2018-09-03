---
title: Redis哨兵模式
tags:
  - redis
copyright: true
date: 2018-08-28 18:18:59
categories: redis
---
# Redis Sentinel 模式简介 #
Redis-Sentinel是官方推荐的高可用解决方案，当redis在做master-slave的高可用方案时，假如master宕机了，redis本身（以及其很多客户端）都没有实现自动进行主备切换，而redis-sentinel本身也是独立运行的进程，可以部署在其他与redis集群可通讯的机器中监控redis集群。
<!-- more -->
# 它的主要功能有一下几点 #
1、不时地监控redis是否按照预期良好地运行;
2、如果发现某个redis节点运行出现状况，能够通知另外一个进程(例如它的客户端);
3、能够进行自动切换。当一个master节点不可用时，能够选举出master的多个slave(如果有超过一个slave的话)中的一个来作为新的master,其它的slave节点会将它所追随的master的地址改为被提升为master的slave的新地址。
4、哨兵为客户端提供服务发现，客户端链接哨兵，哨兵提供当前master的地址然后提供服务，如果出现切换，也就是master挂了，哨兵会提供客户端一个新地址。

# 哨兵（sentinel）本身也是支持集群的 #
很显然，单个哨兵会存在自己挂掉而无法监控整个集群的问题，所以哨兵也是支持集群的，我们通常用三台哨兵机器来监控一组redis集群。
# 快速开始！ #
## 环境准备： ##
centos7服务器3台,6也可以，没什么区别。
我部署好了三台
redis-1
redis-2
redis-3
清空selinux与iptables

##  编译安装redis ##
```
yum install gcc* tcl -y
mkdir /opt/soft
cd /opt/soft
wget http://download.redis.io/releases/redis-3.2.4.tar.gz
tar xf redis-3.2.4.tar.gz
cd redis-3.2.4
make
mkdir conf
mkdir bin
cp utils/redis_init_script bin/redis
cp redis.conf conf/6379.conf
cd ..
mv redis-3.2.4 /opt/redis
cd /opt/redis/bin
sed -i s#CLIEXEC=\/usr\/local\/bin\/redis-cli#CLIEXEC=\/opt\/redis\/src\/redis-cli#g redis
sed -i s#EXEC=\/usr\/local\/bin\/redis-server#EXEC=\/opt\/redis\/src\/redis-server#g redis
sed -i s#CONF=\"\/etc\/redis#CONF=\"\/opt\/redis\/conf#g redis
cd /opt/redis/conf
sed -i s#daemonize\ no#daemonize\ yes#g 6379.conf
```
## 安装完毕后，修改配置文件。 ##
vim /opt/redis/conf/6379.conf
注释此条
bind 127.0.0.1
protected-mode yes 改为 protected-mode no #关闭安全模式
至此，redis部署完毕。


redis的启动停止脚本在
/opt/redis/bin/redis stop/start
redis的配置文件在
/opt/redis/conf/6379.conf
redis的登陆命令在
/opt/redis/src/redis-cli

 ## redis配置主从 ##

启动两台redis
redis-1	10.0.0.10
redis-2 10.0.0.20

若redis-1为主的话，在redis-2的配置文件中配置
slaveof 10.0.0.10 6379
修改完毕后重启redis即可，重启后我们可通过登陆进入redis后info查看主从信息。

## 引入哨兵。 ##

redis-1与redis-2搭建完毕主从后，我们开始引入哨兵。
哨兵是一个单独的程序，所以我们需要单独部署它。
若是在其他机器上部署哨兵，那么请用上面的redis安装脚本重新安装一遍redis。
在这里我已经部署完毕了
redis-1
redis-2
redis-3
## 增加哨兵的配置文件。三台redis都需要增加，文件内容这三台一样。 ##
```
vim /opt/redis/conf/sentinel.conf
##sentinel for  10.0.0.10 ,its slave is 10.0.0.20
#master1
port 26383
sentinel monitor master1 10.0.0.10 6379 2
sentinel down-after-milliseconds master1 30000
sentinel failover-timeout master1 900000
sentinel parallel-syncs master1 1
#sentinel auth-pass mymaster 123456　　#如果你的redis集群有密码
```
配置文件的含义请自行百度。

## 启动哨兵 ##
三台机器都是一个操作方式。


添加窗口
screen -S sentinel


在新窗口启动哨兵
/opt/redis/src/redis-sentinel /opt/redis/conf/sentinel.conf --protected-mode no
启动后即可看到前台输出信息。


后台挂起这个窗口请按：
Ctrl+a+d


下次返回观看这个窗口请输入
screen -r sentinel


我们这里暂时不挂起窗口，可以观察哨兵监控集群的状态。

我们接下来我们进行切换以及增加新的salve节点测试。
关掉redis-1并查看哨兵监控的状态，约30秒内，哨兵探测redis-1客观故障后，即会重新选举新的master，重新选举完毕后我们在redis-2中info查看主从状态，会发现redis-2已经被选举为master。
重新启动redis-1，并不需要修改配置文件，启动后redis-1自动会与redis-2进行同步。

修改redis-3的配置文件，把slaveof指向到redis-2，启动后你会发现哨兵会把redis-3自动添加到集群中。

原文：https://www.cnblogs.com/kerwinC/p/6069864.html

 