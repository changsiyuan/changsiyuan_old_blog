---
layout : post
title : MapReduce过程名词解释
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- 在mapreduce的运行过程中，有很多精巧的机制保证整个作业的高效率、公平和高容错性。其中又很多容易混淆的名词。本文通过罗列这些名词的含义来对这些过程加以解释说明。

### 名词解释
|名词|含义|
|---|---|
|shuffle|将map的输出作为输入传给reducer的过程的总称；|
|spill|map的结果从内存往磁盘写数据的过程；中文译为“溢写”，但并不一定是当内存溢出后才写入磁盘；|
|partitioner|指partitioner接口，作用是根据key或value及reduce的数量决定当前的这对输出数据<key value>应交给哪个reduce task处理；|
|sort|指快速排序；当map内存占用达到一定比例时，需对于内存中的key做sort，以便combiner过程；|
|combiner|将一个map内存中具有相同<key,value>对的value加起来，以减少spill到磁盘的数据量；将已经写到磁盘的文件进行上述动作也称为combiner；|
|merge|若map产生的数据量较大，一个map会有多个spill过程从而在磁盘中产生多个spill文件。将磁盘中多个spill文件合并成一个的过程称为spill；merge的本质是合并文件的过程，具有相同key的value并没有在merge过程中相加，而是在combiner中实现；|
