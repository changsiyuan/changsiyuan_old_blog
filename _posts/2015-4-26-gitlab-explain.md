---
layout : post
title : Gitlab基本原理
category : git
tagline : "author: ChangSiyuan"
tags : [git]
---
{% include JB/setup %}


### 引言
- gitlab是常用的版本管理工具。
- 相对于github来说，gitlab不收费，而且基本具有github的全部功能，因此特别适合小微企业和学校实验室等用作版本管理工具。
- gitlab具有比svn强大多的功能，在代码控制、版本控制、任务进度控制、甚至工资绩效等方面发挥着重大作用。
- gitlab中有很多容易混淆的过程和名字，本文解释了gitlab的运行过程，可作为初学者的参考。

### 本地和远端
- 特别要注意的是，本地和远端remote是相对的概念。一般来说将PC作为本地，服务器作为远端。事实上，普通的PC也可以作为远端，本地和远端的地位是平行的，不存在远端地位较高只说。
- 远端的名字默认为origin，所以push的时候一般用命令`git push origin master`，即将本地的内容拉取到名字为origin的远端的master分支上去。如果远端名称不叫origin，则命令要相应变化。

### 本地和远端的交互动作
- 下图是网上找到的一张图，清晰地描绘了git本地和远端的交互过程：

![git](https://raw.githubusercontent.com/changsiyuan/changsiyuan.github.io/master/_image/git.png)

- fetch：将本地已有的工程从远端拉取到本地repository；
- clone：将本地没有的工程从远端拉取到本地repository；
- pull：直接将本地没有的工程从远端拉取到本地工作目录；
- checkout：将本地repository存储的某个版本作为工作目录；
- add：将本地的更改添加到索引中；
- commit：将索引中的修改一次性写入本地repository；
- push：将repository中的更改拉取到远端，实现版本非本地端备份；
- checkout和commit是相反动作，clone/fetch和push是相反动作；

### 版本管理动作
- 上图中没有的一些动作，对于版本管理也必不可少，下面将介绍。
- branch：分支，分支的作用是方便大家同时开发不同的模块。
- merge：将两个分支合并，比如自己的分支和master合并。
- 哈希值：gitlab会自动为每个版本计算哈希值，用哈希值来识别版本。

### gitlab常用命令
- `git clone url`：clone到本地；
- `git add .`：添加改动到index;
- `git commit -a -m "..."`：添加改动到本地库；
- `git push origin master`：这个命令上面已经说过，将本地某个版本push到远端，实现版本备份；

### gitlab中经典的开发场景解析
- 最常见的开发场景就是用netbeans中代码管理，下面是流程。
- 第一步：在远端建立自己的分支；
- 第二步：将这个分支clone到本地（netbeans中team菜单项>git>clone），注意要将master和自己的分支都clone下来；
- 第三步：选择netbeans菜单项team>git>repository browser，右键单击remote里面的自己的分支，选择checkout revision，就将本地工作目录设定为了自己的分支；
- 第四步：按照要求修改代码；
- 第五步：修改完成代码后，右键单击工程，选择git>commit，然后提交commit信息，将改动添加到本地库；
- 第六步：commit后，右键单击工程，选择git>remote>push，然后将本地的修改push到远端备份；
- 第七步：在远端提交merge request，等待上级领导的审阅和合并通过。到此为止，一个完整的有gitlab版本控制参与的开发流程就结束了！


