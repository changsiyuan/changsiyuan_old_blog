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
- 本文重点介绍了node.js的开发环境搭建；


