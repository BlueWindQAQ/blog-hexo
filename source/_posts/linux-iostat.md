---
title: iostat数据中关于rKB/s 和wKB/s 列解读
tags:
  - linux
  - I/O
copyright: true
date: 2018-08-29 20:42:12
categories: linux
---
导引
天天遇到这两个参数,始终搞不懂,究竟怎么样才算是读写高<!-- more -->
```
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          36.04    0.00   14.47    1.78    0.00   47.72

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
vda               0.00     0.00  125.00  278.00  2000.00 11617.00    67.58     0.55    1.36    1.07    1.49   0.77  31.20

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          23.65    0.00    7.46    1.29    0.00   67.61

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
vda               0.00     0.00   71.00  271.00  1136.00 11287.00    72.65     0.53    1.55    1.17    1.65   0.77  26.40
```

含义:

> 而`rKB/s` 和`wKB/s` 列:
每秒千字节为单位显示了读和写的数据量 如果这两对数据值都很高的话说明磁盘io操作是很频繁(注:高是怎么衡量的?)
 
两对数据值都很高的话说明磁盘io操作是很频繁