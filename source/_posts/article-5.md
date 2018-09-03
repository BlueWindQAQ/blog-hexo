---
title: maven项目引入外部jar
copyright: true
date: 2018-07-12 18:52:05
tags: 
	- maven
	- jar
categories: maven
---
当我们开发一个功能时发现自己的`maven`仓库中缺少需要的jar怎么办？
- 首先将需要的jar下载下来
- 然后将jar导入到项目中：webapp/WEB-INF/lib目录下
- 最后在`pom.xml`文件中加入依赖就可以编译和打包运行了
<!-- more -->
```
<dependency>
  <groupId>fakepath</groupId>
  <artifactId>jxl-report</artifactId>
  <version>1.0</version>
  <scope>system</scope>
  <systemPath>${basedir}/src/main/webapp/WEB-INF/lib/jxl-report-1.0.jar</systemPath>
</dependency>
```
systemPath：导入外部jar的路径
${basedir}：项目根目录
最终路径为：${basedir}/src/main/webapp/WEB-INF/lib/xxx.jar
