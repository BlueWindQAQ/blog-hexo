---
title: hexo添加右侧公众号
tags:
  - hexo
copyright: true
date: 2018-08-23 10:09:36
categories: hexo
---
hexo添加右侧公众号页面需在两处添加代码
1、css
css添加在主题文件下的source/css/_schemes/主题名称文件夹/_layout<!-- more -->
```
.paral
{
  position: absolute; 
  top: 10%;  
  right: 0; 
  width:200px; 
  height:140px;
  -webkit-user-select:none;   ##禁止鼠标选中
  -moz-user-select:none;
  -ms-user-select:none;
  user-select:none;
}
.zi
{
  position: absolute;  
  right: 0; 
  width:30px; 
  height:120px;
  writing-mode: vertical-lr;   ##文字垂直
  text-align:center;   ##文字居中
  background-color:#e2e5b7;
  font-size:18px;
  cursor:pointer;   ##划过时鼠标样式
  top:50%;
  transform:translateY(-50%);  ##p标签垂直居中
  border:1px solid #d1d8bd;
  border-radius:5px;   ##边框圆角
}
.img
{
  position: absolute;   
  right: 60px; 
  width: 140px;
  height: 140px;
  display: none;   ##默认不显示图片
}
p:hover +.img {
    display: block;  ##鼠标划过时，改变class--img属性为显示
}
```
2、HTML
HTML代码添加到主题文件夹下layout/_layout.swig中class为main中
```
<div class="paral">
  <p class="zi">公众号</p>
    <img src="/uploads/qrcode_for_smartfoot.jpg" class="img">
</div>
```