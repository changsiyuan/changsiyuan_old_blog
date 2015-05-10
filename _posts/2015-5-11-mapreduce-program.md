---
layout : post
title : MapReduce编程技巧和注意事项
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- 在编写Mapreduce程序的过程中会遇到各种各样的问题，本文为您梳理了常见的问题、注意事项和技巧；

### MapReduce中常见的数据类型转换方法

| 类型转换        | 方法汇总           | 
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
例如：`Set <Text> userSet = new HashSet<Text>();`  或者  `List <LongWritable> list = new ArrayList <LongWritable>();`
所以尽量不要同时使用；

- reduce中遍历values的过程只能执行一遍，因为values是Iterable迭代器类型；如果需要多次遍历values内容，要么每次遍历前移动迭代器指针到初始位置，要么第一次遍历时将values内容放入list中，方便以后重复访问；

- map的输出必须是Writable类型的（比如IntWritable、LongWritable、Text），如果是普通java类型（Integer、Long、String）会出错（报错：Unable to initialize MapOutputCollector）；

- map的输入固定为<key=这一行的输入在分片文件中的偏移量，value=这一行内容>，输入类型为<object类型，Text类型>，这些都不能随意更改，如需更改，要重写inputFormat；

### Mapreduce程序参数设置

| 参数        | 设置方法           | 
| ------------- |:-------------:|
|不执行reduce函数 |job.setNumReduceTasks(0); |
|设置执行map的类 |job.setMapperClass(FlowFilter.class); |
|设置执行combine的类（一般就是reduce类）|job.setCombinerClass(Reduce.class);|
|设置执行reduce的类|job.setReducerClass(DNTGUserInfoReducer.class);|
|设置map的数量|job.setNumMapTasks(maps)；|
|设置reduce数量（最终输出文件数量）|job.setNumReduceTasks(reduces);|
|设置文件输入类型（默认为inputFormat，一行一行读取）|job.setInputFormatClass(ProvinceInputFormat.class); |
|设置map的输出类型（key、value类型）|job.setMapOutputKeyClass(HMKey.class); job.setMapOutputValueClass(HMValue.class);|  
|设置redcue的输出类型（key、value类型）|job.setReduceOutputKeyClass(Text.class); job.setReduceOutputValueClass(IntWritable.class);|
|统一设置map和reduce的输出类型|job.setOutputKeyClass(Text.class); 
job.setOutputValueClass(IntWritable.class);|
|设置整个程序输入、输出路径|FileInputFormat.addInputPath(job, new Path(otherArgs[0])); FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));|
