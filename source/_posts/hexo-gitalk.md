---
title: Hexo NexT主题中集成gitalk评论系统
tags:
  - hexo
copyright: true
date: 2018-08-23 13:56:11
categories: hexo
---
记录在NexT主题中添加gitalk评论系统的步骤。
gitalk：一个基于 Github Issue 和 Preact 开发的评论插件
详情Demo可见：https://gitalk.github.io/<!-- more -->
# Register Application #
在GitHub上注册新应用，链接：https://github.com/settings/applications/new
![](https://i.imgur.com/3bJcfR3.png)
参数说明：
Application name： # 应用名称，随意
Homepage URL： # 网站URL，如https://asdfv1929.github.io
Application description # 描述，随意
Authorization callback URL：# 网站URL，https://asdfv1929.github.io

点击注册后，页面跳转如下，其中`Client ID`和`Client Secret`在后面的配置中需要用到，到时复制粘贴即可：
![](https://i.imgur.com/ljehYsA.png)
# gitalk.swig #
新建`/layout/_third-party/comments/gitalk.swig`文件，并添加内容：
```
{% if page.comments && theme.gitalk.enable %}
  <link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
  <script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
   <script type="text/javascript">
        var gitalk = new Gitalk({
          clientID: '{{ theme.gitalk.ClientID }}',
          clientSecret: '{{ theme.gitalk.ClientSecret }}',
          repo: '{{ theme.gitalk.repo }}',
          owner: '{{ theme.gitalk.githubID }}',
          admin: ['{{ theme.gitalk.adminUser }}'],
          id: location.pathname,
          distractionFreeMode: '{{ theme.gitalk.distractionFreeMode }}'
        })
        gitalk.render('gitalk-container')           
       </script>
{% endif %}
```
# comments.swig #
修改`/layout/_partials/comments.swig`，添加内容如下，与前面的`elseif`同一级别上：
```
{% elseif theme.gitalk.enable %}
 <div id="gitalk-container"></div>
```
# index.swig #
修改`layout/_third-party/comments/index.swig`，在最后一行添加内容：
```
{% include 'gitalk.swig' %}
```
# gitalk.styl #
新建`/source/css/_common/components/third-party/gitalk.styl`文件，添加内容：
```
.gt-header a, .gt-comments a, .gt-popup a
  border-bottom: none;
.gt-container .gt-popup .gt-action.is--active:before
  top: 0.7em;
```
# third-party.styl #
修改`/source/css/_common/components/third-party/third-party.styl`，在最后一行上添加内容，引入样式：
```
@import "gitalk";
```
# _config.yml #
在主题配置文件`next/_config.yml`中添加如下内容：
```
gitalk:
  enable: true
  githubID: github帐号  # 例：asdfv1929   
  repo: 仓库名称   # 例：asdfv1929.github.io
  ClientID: Client ID
  ClientSecret: Client Secret
  adminUser: github帐号 #指定可初始化评论账户
  distractionFreeMode: true
```
以上就是NexT中添加gitalk评论的配置，博客上传到GitHub上后，打开页面进入某一博客内容下，就可看到评论处。

部分问题解决方法，可参见：
https://liujunzhou.cn/2018/8/10/gitalk-error/#more

原文：https://asdfv1929.github.io/2018/01/20/gitalk