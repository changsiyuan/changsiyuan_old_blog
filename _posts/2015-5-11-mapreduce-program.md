---
layout : post
title : MapReduce设计模式实例解析
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- 在编写Mapreduce程序的过程中会遇到各种各样的问题，本文为您梳理了常见的问题、注意事项和技巧；

### MapReduce中常见的数据类型转换方法
| Tables        | Are           | 
| ------------- |:-------------:|
| int>String  | int i=12345; String s=""; 第一种方法：s=i+""; 第二种方法：s=String.valueOf(i);| 
| String>int   | s="12345"; int i; 第一种方法：i=Integer.parseInt(s); 第二种方法：i=Integer.valueOf(s).intValue();|   
| LongWritable>String | LongWritable.toString()|  
|String>long|Long.parseLong(String)|
|Text>String|Text.toString()|
|long>LongWritable|new LongWritable(long)|
|String>Text|new Text(String)|

### 编程注意事项
- hadoop特有的数据类型（LongWritable、IntWritable和Text等）和java的数据结构（set、list、map等）不兼容，以下使用情形会出错：
例如：Set <Text> userSet = new HashSet<Text>();  或者  List <LongWritable> list = new ArrayList <LongWritable>();
所以尽量不要同时使用；

- reduce中遍历values的过程只能执行一遍，因为values是Iterable迭代器类型；如果需要多次遍历values内容，要么每次遍历前移动迭代器指针到初始位置，要么第一次遍历时将values内容放入list中，方便以后重复访问；

- map的输出必须是Writable类型的（比如IntWritable、LongWritable、Text），如果是普通java类型（Integer、Long、String）会出错（报错：Unable to initialize MapOutputCollector）；
