##1.基本的概念
***

>MapReduce有两个版本。

>MRv1是Hadoop 1.0中的MapReduce实现，它由编程模型（新旧编程接口）、运行时环境（由JobTracker和TaskTracker组成）和数据处理引擎（MapTask和ReduceTask）三部分组成。

>MRv2是Hadoop 2.0中的MapReduce实现，它在源码级重用了MRv1的编程模型和数据处理引擎实现，但运行时环境由YARN和ApplicationMaster组成。

本系列主要是分析MapReduce框架中的数据处理引擎，简要的介绍以下编程模型，至于如何搭建Hadoop，运行时的环境，如何编程什么的都不会介绍。
。

***
##2.整体流程
***

MapReduce分为以下几个阶段（TaskStatus）
* STARTING
* MAP
* SHUFFLE
* SORT
* REDUCE
* CLEANUP

其中SHUFFLE最为复杂，这也最重要的地方。

####WordCount为例，处理的流程

一般来讲，MapReduce的框架在处理数据的时候是这样的。以WordCount为例，一个文本变成了多个(K1,V1)，然后自己可以在map中处理这个键值对，生成一个新的键值对(K2,V2)，在reduce中又可以处理一个新的键值对(K2，list<V2>)，处理的结果会变成文本输出。

####一些小问题

1. 一个文本如何变成键值对的，处理完之后的键值对又是如何变成了一个文本输出的？
2. 如何实现的并行处理文件，就是如何分配一个文件给不同的map来处理？
3. 这个框架分为几个阶段，每个阶段的作用？
4. map只能是处理一个键值对，那么框架中是如何不断的使用map来处理多个键值对的；reduce也只能处理一个键值对，那么框架中是如何不断的使用reduce来处理多个键值对的？

####shuffle流程中的问题

1. map的输出是如何管理的，reduce的输入又是从何而来，两者之间有什么关系？
2. map输出是(K,V)，那么为什么reduce的输入是(K,list<V>)？
3. 为什么要排序，如何实现的排序？
4. 什么是merge，为什么要merge,merge和spill有什么关系？
5. map reduce combine有什么关系？

***
##3.本系列的顺序
***

####map
* map的输入TextInputFormat
* map的数据处理流程Mapper、MapTask
* map的输出MapOutputBuffer

####reduce
* reduce的输入
* reduce的数据处理流程Reducer、ReduceTask
* reduce的输出TextOutputFormat

####一些辅助类
* Merge
* Partition
* Combine
* Serialize
* Job

####总结
* sort 函数、sort的效果、sort phase
* sort、spill、merge的关系
* map reduce combine
* MapReduce框架回顾
