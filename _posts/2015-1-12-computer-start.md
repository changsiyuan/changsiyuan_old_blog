---
layout : post
title : 计算机开机过程详解
category : Computer Harware
tagline : "author: ChangSiyuan"
tags : [computer]
---
{% include JB/setup %}

### 引言
- 计算机开机过程似乎是个非常复杂的过程，本文为您梳理出计算机开机最重要的若干阶段，让您对计算机开机过程一目了然；
- 参考资料：[计算机是如何启动的](http://www.ruanyifeng.com/blog/2013/02/booting.html)

### 第一阶段：BIOS
- 按下开机键，读取ROM内容；
- 硬件自检，屏幕会显示检测的CPU、内存、硬盘的信息，如果有错则蜂鸣；
- 按照用户的设定，决定计算机从哪里启动（是从硬盘、还是从DVD、还是从优盘），这一步之前用户可以通过长按F12修改从哪里启动；

### 第二阶段：主引导记录
- 磁盘逻辑结构见下图：

![disk](https://raw.githubusercontent.com/changsiyuan/changsiyuan.github.io/master/_image/disk.png)

- 决定从硬盘启动；
- 从硬盘的0号扇区读取主引导记录（master boot record，MBR）；
- MBR结尾是分区表(上图)，告诉计算机每个分区的起始和终止地址；
- 确定活动分区（装有操作系统的分区），读取这个分区的引导块（第一个块），执行它；
- 引导块中的程序负责装载该分区的操作系统；
- 操作系统启动，加载操作系统内核，启动任务管理器等；
