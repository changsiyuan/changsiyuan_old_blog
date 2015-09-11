---
layout : post
title : Web技术名词解释
category : Web
tagline : "author: ChangSiyuan"
tags : [Web]
---
{% include JB/setup %}

## 引言
- web开发框架中有很多技术，这些技术支撑着web开发向前发展。我们今天看到的各种各样的漂亮的网页也是在这些技术下得以实现的。本文梳理了web开发中的常用技术，作为参考。

## 浏览器端

### 基础语言框架
- html：页面显示语言，浏览器识别并解析；
- javascript：前端开发用的轻量级脚本语言，用于页面的控制和内容获取；
- E4X：javascript的扩展，向JavaScript添加了对 XML 的直接支持，是正式的javascript标准之一；

### 页面渲染技术
- css（Cascading Style Sheets）：层叠样式表技术，用于新框架下的页面渲染；
- less：一种动态的css语言，在浏览器端解析；
- sass：一种动态的css语言，在服务器端解析；
- bootstrap（twitter支持）：由less编写的一套页面渲染框架（样式库），栅格系统和屏幕适配性是它的优势所在；

### Javascript常用IDE
- Sublime Text ；
- WebStorm ；
- netbeans；
- Brackets ；
- Atom；

### 常用Javascript辅助工具
- grunt：自动完成js代码合并压缩等，支持自动化测试；
- node.js：充当浏览器的js解析器的功能，还可以外加很多插件，方便调试；
- bower：基于node.js，依赖管理工具，自动下载、管理、监测package的依赖关系；
- HTTP-server：基于node.js，轻量级的服务器，用于通过http协议访问本机文件夹；
- KARMA/Jasmine：基于node.js，前端代码单元测试工具（相当于java的JUnit），KARMA提供测试工具驱动，Jasmine提供测试语法；
- protractor：针对angularjs的自动化测试工具，不能用于其他js库；
- selenium：Web应用程序测试的工具，自动完成用户操作以便测试网站，可用于轮播系统的制作；

### 前端网页开发框架
|框架名称|描述|优缺点|依赖|
|---|---|---|---|
|angularjs|免费，google支持；辅助JavaScript的网页开发框架；和ExtJS处于竞争地位；|灵活性高但是完备性中等；UI控件库不如ExtJS全面，但是可定制性强；不直接支持移动端；|由于没有全面的UI控件，一般用angularUI或者bootstrap进行页面布局和渲染；|
|ExtJS|收费；辅助JavaScript的网页开发框架；和angularjs处于竞争地位；|完备性高但是灵活性中等；拥有非常全面的UI控件库，浏览器间可移植性极强，但是用户定制性稍差；直接支持移动端；||
|jQueryUI|免费；|UI控件较为混乱；可定制性强；直接支持移动端；||
|NEJ(Nice Easy Javascript)|网易前端开发框架；辅助JavaScript的网页开发框架；|||

### 前端框架支持库
|支持库名称|描述|优缺点|
|---|---|---|
|jQuery|纯粹的JavaScript代码库，目的是简化DOM操作|和prototype做的事情相同，方法不同，jQuery火了，prototype没有火起来；|
|prototype|纯粹的JavaScript代码库，目的是简化DOM操作|没有jQuery火|
|YUI(Yahoo! UI Library)|开放源代码的 JavaScript 函数库；||

### 异步请求技术
ajax（asynchronous javascript and xml）：此技术破除了旧的web框架“只能请求一次”的限制，使得程序员可以指定浏览器什么时候发出请求、发出什么请求、请求内容是什么，ajax已经封装在前端框架中（诸如angularjs等框架），无需人为添加库；

## 服务器端

### 基础语言
- php：开源脚本语言，用于小型web开发，服务器端语言，比java的学习成本和复杂度低得多，但是功能有限，比较简陋；
- java：后台可以用java去写；
- jsp（Java Server Pages）：动态网页技术标准，用于大型web开发，语言是在html中插入java；

### SSH框架
- Struts2（view）：对浏览器请求处理的一整套框架；
- Spring（control）：提供了依赖注入的思想，提供了大量模板类，配合和支持Struts2完成对用户请求的页面响应；
- Hibernate（model）：面向java环境的对象/关系数据库映射工具，封装了和数据库的连接部分；
- 这种老框架的劣势：框架太多、太复杂；

### RESTful框架
- RESTful（用java实现的话叫做JAX-RS）：
  - 基于HTTP协议的（get put post等方法），一种分层的目录型的设计风格（任何资源可被唯一的URI访问到），支持JSON和XML格式的数据，在javaEE中具体表现为REST.java文件；
  - 它之所以叫做RESTful指的是server端不用承担全部的页面内容传递和逻辑的全部任务，显示逻辑交由client端，剩下的（rest）交给server端；
- ORM（Object Relational Mapping，用java实现的话叫做JPA）：
  - 一种在面向对象语言和数据库等数据源之间传递数据的桥梁，作用是数据映射，在javaEE中具体表现为entity.java文件；
- JAX-RS（Java API for RESTful Web Services）：RESTful设计风格在java中的具体表现，在java中具体表现为REST.java文件；
- JPA（java persistence API）：ORM标准在java中的具体表现，在java中具体表现为entity.java文件；

### 服务器软件
- apache：跨平台，安全性高；
- tomcat：免费的开放源代码的Web 应用服务器，属于轻量级应用服务器，适合中小企业；
- glassfish：一款强健的商业兼容应用服务器，达到产品级质量，可免费用于开发、部署和重新分发；

### 数据格式
|数据格式名称|描述|
|---|---|
|JOSN|一种数据格式，新版框架下浏览器请求数据时，服务器会返回这种格式的数据，可用javascript解析，可用ajax技术传输；|
|XML|一种数据格式，比JOSN重，面向数据传输；|

### 设计思想
|设计思想|描述|
|---|---|
|DOM框架|是一种页面元素控制的设计思想，他的树形结构便于获取页面元素；|
|MVC框架|是一种前端到后端的设计思想，model、view、controller三者分离，要什么请求什么，更加灵活，易于扩展和测试；|
|同源策略|网景公司（Netscape）提出的著名的安全策略，禁止服务器跨域读取数据；|

### 组织团体
- W3C（World Wide Web Consortium）：万维网联盟的缩写，是对网络标准制定的一个非赢利组织，致力在万维网发展方向上达成共识；


