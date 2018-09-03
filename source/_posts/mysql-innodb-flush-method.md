---
title: 针对innodb_flush_method参数的理解和对比测试（mycat+mysql）
tags:
  - mysql
copyright: true
date: 2018-08-30 16:04:01
categories: mysql
---
mysql的`innodb_flush_method`这个参数控制着innodb数据文件及redo log的打开、刷写模式，对于这个参数，文档上是这样描述的：
有三个值：fdatasync(默认)，O_DSYNC，O_DIRECT<!-- more -->
默认是fdatasync，调用fsync()去刷数据文件与redo log的buffer
为O_DSYNC时，innodb会使用O_SYNC方式打开和刷写redo log,使用fsync()刷写数据文件
为O_DIRECT时，innodb使用O_DIRECT打开数据文件，使用fsync()刷写数据文件跟redo log
首先文件的写操作包括三步：open,write,flush
上面最常提到的fsync(int fd)函数，该函数作用是flush时将与fd文件描述符所指文件有关的buffer刷写到磁盘，并且flush完元数据信息(比如修改日期、创建日期等)才算flush成功。
使用O_DSYNC方式打开redo文件表示当write日志时，数据都write到磁盘，并且元数据也需要更新，才返回成功。
O_DIRECT则表示我们的write操作是从MySQL innodb buffer里直接向磁盘上写。

这三种模式写数据方式具体如下：

fdatasync模式：写数据时，write这一步并不需要真正写到磁盘才算完成（可能写入到操作系统buffer中就会返回完成），真正完成是flush操作，buffer交给操作系统去flush,并且文件的元数据信息也都需要更新到磁盘。
O_DSYNC模式：写日志操作是在write这步完成，而数据文件的写入是在flush这步通过fsync完成
O_DIRECT模式：数据文件的写入操作是直接从mysql innodb buffer到磁盘的，并不用通过操作系统的缓冲，而真正的完成也是在flush这步,日志还是要经过OS缓冲
![](http://cloud.bestsmartfoot.top/images/blog/hexo-mysql-innodb-flush-method-1.png)

原文：https://blog.csdn.net/smooth00/article/details/72725941