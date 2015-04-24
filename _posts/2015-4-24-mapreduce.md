---
layout : post
title : MapReduce过程详解
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- mapreduce的过程看似简单，其实，要保证数据处理过程中的高效率、高容错性，mapreduce中有很多精巧的机制值得我们学习和参考。本文主要介绍mapreduce的过程以及其中涉及到的重要的思想。

### Mapreduce总体过程
- 作业启动
- 作业初始化
- 作业/任务调度
- Map任务执行
- shuffle
- Reduce任务执行
- 作业完成

### 作业启动
- 用户编写mapreduce程序，并且打成jar包，将jar包放在本地集群，将输入数据放在HDFS上面；
- 用户执行”hadoop jar“命令，启动作业；
- JobClient是大boss，他收到任务后，向”业务主管“JobTracker申请新的作业ID，YARN界面上显示的ID即job ID；
- JobClient检查输入输出路径、检查这个路径的权限等，如果一切正常则开始下一步；

### 作业初始化
- JobTracker可能会收到JobClient不同作业的请求，维护一个队列处理这些请求，交由作业调度器调度作业（见【附录--作业调度机制】）；
- 分片数量由用户指定（见【附录--map数量的确定】），分片数量等于map数量，JobTracker根据map数量创建一批map任务；

### 作业/任务调度
- map任务分配是被动的而不是主动的。TaskTracker启动后，通过心跳和JobTracker保持联系并查询是否有任务可做，如果有任务则被分配任务；
- 每个TaskTracker有固定数量的map任务槽和reduce任务槽若干个，分别用来接收map和reduce任务，系统会自动优先填满map任务槽，待map任务槽被填满后才会被分配reduce任务（因为map的优先级总是高于reduce）；

### Map任务执行
- 某个TaskTracker领取map任务；
- 这个TaskTracker将作业的jar包文件和相关配置文件复制到本地工作目录下（传输jar包而不传输数据的原则，代码靠近数据的原则）；
- TaskTracker启动单独的JVM运行这个map任务；
- map的输出结果中相同的key不一定在一块，为了下面的combine过程简洁，这里对map输出结果中的key进行快速排序（sort过程）；
- map任务的结果存入内存，并在内存有限的情况下定期写入磁盘（spill过程），将某一个map的输出的相同的key的value合并（即combine过程），spill与combine经常交替进行；
- 多次spill会产生许多小文件在磁盘中，merge过程将他们合并；
- TaskTracker和JobTracker定期通信，报告进度；

### shuffle
- map的输出是（key, value）对（下面简称KV对）；
- 将某一个map的输出的相同的key的value合并，即combine过程；
- 决定这个map的结果给哪一个reduce：通过hash(key)mod(reduce数目)计算出partition的ID，上述的key就被分配给这个partition；一个partition中可以有多个key，但是同一个key只存在于一个partition中；一个partition对应一个reduce；
- 有的partition负载可能很重，有的则很轻，需要通过一定的协调机制平衡负载（比如根据自己的需求重写partition函数）；
- partiton作为reduce的输入，每个reducer获得的是每个Key在在每个mapper上输出的结果，它需要使用reduce函数把相同Key的不同mapper的输出统计在一起。

### Reduce任务执行
- 在部分map任务执行完后（不用等到所有map任务结束）JobTracker开始分配reduce任务到TaskTracker；
- TaskTracker启动单独的JVM运行这个reduce任务；
- TaskTracker从远地下载中间结果文件到本地（指partition文件、一个partition对应一个reduce），为reduce任务真正开展做准备，但不会开始执行reduce()函数；
- 待所有的map任务都完成以后，JobTracker通知所有的TaskTracker开始做reduce任务；
- TaskTracker和JobTracker定期通信，报告进度；

### 作业完成
- 每个TaskTracker都将reduce任务的结果文件放到HDFS的临时文件中；
- 当所有reduce任务完成后，这些临时文件会合并成最终输出文件放在用户指定的输出目录下；
- JobTracker收到所有任务的完成通知后，并通知JobClient作业已经完成，YARN管理系统会显示已完成的信息；

### 附录一：map数量的确定
- 用户通过指定期望的map数和期望的分片最小值来调控map数量，具体是系统通过下面几步算出map个数；

![map num](https://raw.githubusercontent.com/changsiyuan/changsiyuan.github.io/master/_image/map%20num.png)

### 附录二：作业调度机制
- JobTracker可同时调度多个作业任务，具体有如下几种调度机制：
- 先进先出调度机制：设定VERY_HIGH、HIGH、NORMAL、LOW、VERY_LOW五个等级的优先级，任务优先级高的先执行，如果优先级相同，先进入调度器的任务先执行；
- 公平调度机制：JobTracker掌管着所有的资源，平均为进入队列的任务分配资源，支持资源抢占；
- 能力调度器：如果一个任务所需资源多，就为他多分配资源，反之少分配资源；

###附录三：JobTracker容错机制
- 心跳机制：JobTracker和TaskTracker定期通信（通过mapred.tasktracker.expiry.interval设定间隔时间，默认为10分钟），TaskTracker报告任务完成情况；未正常返回的任务，JobTracker会重新分配TaskTracker去完成；
- 黑名单机制：每个job维护一个TaskTracker黑名单，一旦TaskTracker无法完成任务即进入黑名单，JobTracker不会再给他分配任务；
- 集群黑名单：如果一个TaskTracker进入job黑名单的次数过多，或者这个TaskTracker进入job黑名单的次数明显超过平均进入次数，则将其加入集群黑名单，以后不再向其分配任务；
- 检错机制：如果JobTracker发现某个TaskTracker不正常或者完成任务速度明天低于平均水平，JobTracker会将这个任务另外交由另外一个TaskTracker去完成，如果这个TaskTracker完成的非常快，则JobTracker认为原来的TaskTracker有问题，遂kill原TaskTracker进程，并且判断是否加入黑名单；





