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
