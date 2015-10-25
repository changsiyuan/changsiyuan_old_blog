---
layout : post
title : node.js开发环境配置
category : web
tagline : "author: ChangSiyuan"
tags : [node.js]
---
{% include JB/setup %}

### 什么是node.js
- 维基百科的介绍：Node.js是谷歌V8引擎、libuv平台抽象层以及主体使用Javscript编写的核心库三者集合的一个包装外壳；
- node.js在国内外的使用情况：
  - linkedin移动版正在向node.js迁移；
  - twitter的消息队列用node.js实现；
  - 知乎、网易的推送信息用node.js实现；
  - 阿里的云计算平台也要部分向node.js迁移；
  - .....
- 上面的介绍听起来很抽象，其实，node.js就是一个能够在服务器端运行javascript的框架，实现了众多web开发者在server端使用javascript开发的梦想；
- node.js有很多优势，比如：
  - 模块化；
  - client和server双向实时通信；
  - 高并发（单线程处理多个请求）；
  - IO非阻塞（一件事情正在完成时可以去干另一件事）；
- node.js的[这些优势](http://blog.jobbole.com/53736/)，使得它的应用场景十分广泛：
  - 聊天；
  - 队列输入；
  - 推送信息；
  - 数据流传输；
  - 代理；
  - 只要是符合“计算简单、高并发、需要client和server双向通信”的业务模型都可以用！
- 本文重点介绍了node.js在windows下的开发环境搭建；

### node.js开发环境配置
- node.js开发环境需要配置如下内容：
  - 在windows上安装Sublime和Webstorm；
  - 在windows上安装VirtualBox和Centos虚拟机；
  - 在windows上安装xShell和xFtp，方便在本机和虚拟机之间传送消息或文件；
  - 在虚拟机上安装Redis和MongoDB；
- 下面会一一讲解这些步骤；

### VirtualBox的安装
- [下载VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- 直接双击安装即可；

### 在VirtualBox中创建虚拟机
- 内存分配1024M；
- 磁盘种类选择默认种类；
- 安装CentOs basic web server版本，同时安装development tolls；
- CentOs7的iso镜像文件可从网上下载，添加到虚拟机配置中；
- 如果你所在的局域网路由器DHCP功能启用了，就设置网卡为桥接网卡，系统会自动分配IP地址；
- 如果你所在的局域网路由器DHCP功能禁用了，就设置网卡为NAT模式，需要进一步进行端口映射的配置（下面会将具体配置方法）；
- 安装完reboot虚拟机；

### 虚拟机的配置
- 网卡自启动的配置：
  - 进入下列配置文件，并将最后一行ONBOOT设置为yes；

```
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

- 



