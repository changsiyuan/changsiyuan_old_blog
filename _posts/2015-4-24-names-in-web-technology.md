---
layout : post
title : Web技术名词解释
category : Web
tagline : "author: ChangSiyuan"
tags : [Web]
---
{% include JB/setup %}

### 引言
- web开发框架中有很多技术，这些技术支撑着web开发向前发展。我们今天看到的各种各样的漂亮的网页也是在这些技术下得以实现的。本文梳理了web开发中的常用技术，作为参考。

### 基础语言框架
- html：页面显示语言，浏览器识别并解析；
- javascript：前端开发用的轻量级脚本语言，用于页面的控制和内容获取；
- E4X：javascript的扩展，向JavaScript添加了对 XML 的直接支持，是正式的javascript标准之一；

### 网页开发框架
|框架名称|描述|优缺点|依赖|
|---|---|---|---|
|angularjs|免费；辅助JavaScript的网页开发框架；和ExtJS处于竞争地位；|灵活性高但是完备性中等；UI控件库不如ExtJS全面，但是可定制性强；不直接支持移动端；|由于没有全面的UI控件，一般用angularUI或者bootstrap进行页面布局和渲染；|
|ExtJS|收费；辅助JavaScript的网页开发框架；和angularjs处于竞争地位；|完备性高但是灵活性中等；拥有非常全面的UI控件库，浏览器间可移植性极强，但是用户定制性稍差；直接支持移动端；||
|jQueryUI|免费；|UI控件较为混乱；可定制性强；直接支持移动端；||
|NEJ(Nice Easy Javascript)|网易前端开发框架；辅助JavaScript的网页开发框架；|||

### 支持库对比
|支持库名称|描述|优缺点|
|---|---|---|
|jQuery|纯粹的JavaScript代码库，目的是简化DOM操作|和prototype做的事情相同，方法不同，jQuery火了，prototype没有火起来；|
|prototype|纯粹的JavaScript代码库，目的是简化DOM操作|没有jQuery火|
|YUI(Yahoo! UI Library)|开放源代码的 JavaScript 函数库；||
