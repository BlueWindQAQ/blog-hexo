---
title: 如何运行jar程序
copyright: true
date: 2018-06-14 15:43:53
tags: jar
categories: linux
keywords: jar,windows,linux,springboot
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在开发过程中我们经常将项目打包成war包，打成war的项目就和可以直接放在tomcat和jetty等中间件中运行。那么jar项目怎么运行呢？最近看到springboot比较流行，好奇下弄了个项目跑了起来，然而springboot是jar项目，直接使用主类的main启动项目，那么打包后的项目怎么运行呢？网上查了查，将启动方法放在这里，以便记忆。
<!--more -->
## Windows系统运行 ##
使用dos命令进入jar所在的目录，直接执行执行命令：
    `java -jar xxx.jar`
可按CTRL + C打断程序运行，或直接关闭窗口，程序退出
## linux系统运行 ##
方式一：
    `java -jar XXX.jar`
特点：当前ssh窗口被锁定，可按CTRL + C打断程序运行，或直接关闭窗口，程序退出
方式二：
    `java -jar XXX.jar &`
特点：当前ssh窗口不被锁定，但是当窗口关闭时，程序中止运行。
方式三：
    `nohup java -jar XXX.jar &`
nohup 意思是不挂断运行命令,当账户退出或终端关闭时,程序仍然运行
当用 nohup 命令执行作业时，缺省情况下该作业的所有输出被重定向到nohup.out的文件中，除非另外指定了输出文件。
方式四：
    `nohup java -jar XXX.jar >temp.txt &`
解释下 >temp.txt
command >out.file
command >out.file是将command的输出重定向到out.file文件，即输出内容不打印到屏幕上，而是输出到out.file文件中。
执行nohup命令会执行失败，这时在命令后面加上2>&1 &即可，执行命名如下：
    `nohup java -jar xxx.jar >logs/log.txt 2>&1 &`
可通过jobs命令查看后台运行任务
    `jobs`
那么就会列出所有后台执行的作业，并且每个作业前面都有个编号。
如果想将某个作业调回前台控制，只需要 fg + 编号即可。
    `fg 23`
查看某端口占用的线程的pid
    `netstat -nlp |grep :9181`