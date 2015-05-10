---
layout : post
title : MapReduce设计模式实例解析
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- MapReduce设计模式（Design pattern）是一套被反复使用、多数人知晓的、经过分类编目的代码设计经验总结；
- 使用设计模式的目的：使用设计模式是为了提高编码效率、提高代码重用率、让代码更容易被他人理解、保证代码可靠性；
- 设计模式千万种，本文为您梳理出来最重要、最常用的几种设计模式，方便理解和掌握；
- 本文的每一种设计模式都搭配了实例程序，以及输入和输出，让您更好更快地理解；

### 计数（Counting）一
- 问题定义：在数据文件中包含大量的记录，每条记录中包含了某个实体的若干数值属性，目标问题是要计算每个实体的某个数值属性的函数表达式值，例如求和、平均值等；
- 具体问题描述：现在有一个英文文件，需要统计其中每个单词的个数（wordcount）；
- 解决方案：在map中分离每个单词，传入reduce，在reduce中将每个单词的value相加；
- 程序：

```
package wordcountTest;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

  public static class Map 
            extends Mapper<LongWritable, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1); // type of output value
    private Text word = new Text();   // type of output key
      
    public void map(LongWritable key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString()); // line to string token
      
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());    // set word as each input keyword
        context.write(word, one);     // create a pair <keyword, 1> 
      }
    }
  }
  
  public static class Reduce
       extends Reducer<Text,IntWritable,Text,IntWritable> {

    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0; // initialize the sum for each keyword
      for (IntWritable val : values) {
        sum += val.get();  
      }
      result.set(sum);

      context.write(key, result); // create a pair <keyword, number of occurences>
    }
  }

  // Driver program
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration(); 
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs(); // get all args
    if (otherArgs.length != 2) {
      System.err.println("Usage: WordCount <in> <out>");
      System.exit(2);
    }

    // create a job with name "wordcount"
    Job job = new Job(conf, "wordcount");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(Map.class);
    job.setReducerClass(Reduce.class);
   
    // uncomment the following line to add the Combiner
    job.setCombinerClass(Reduce.class);
     
    // set output key type   
    job.setOutputKeyClass(Text.class);
    // set output value type
    job.setOutputValueClass(IntWritable.class);
    //set the HDFS path of the input data
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    // set the HDFS path for the output
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));

    //Wait till job completion
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}

```

- 示例输入输出：

```
输入：一篇英文文章，单词之间用空格分割
apple banana banana yellow
yellow orange apple orange

输出：词频统计结果
apple 2
banana 2
yellow 2
orange 2
```

### 计数（Counting）二
- 具体问题描述：输入文件为两列，分别为用户手机号和流量数，现需要统计每个用户的总流量数；
- 解决方案：map中将每个用户的手机号、流量分别作为key和value传入reduce，由reduce来统计总流量数；
- 程序：

```
package wordcountTest;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Counter;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

  public static class Map 
            extends Mapper<LongWritable, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1); // type of output value
    private Text user = new Text();   // type of output key
    private IntWritable flow = new IntWritable();
      
    public void map(LongWritable key, Text value, Context context
                    ) throws IOException, InterruptedException 
    	String line = value.toString();
    	String [] array = line.split("\t");
    	if(array.length==2){
    		user.set(array[0]);
    		flow = new IntWritable(Integer.parseInt(array[1]));
    		context.write(user, flow);
    	}else{
    		return;
    	}
    }
  }
  
  public static class Reduce
       extends Reducer<Text,IntWritable,Text,IntWritable> {

    private IntWritable result = new IntWritable();
    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0; // initialize the sum for each keyword
      for (IntWritable val : values) {
        sum += val.get();  
      }
      result.set(sum);

      context.write(key, result); // create a pair <keyword, number of occurences>
    }
  }

  // Driver program
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration(); 
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs(); // get all args
    System.out.println(System.getenv("HADOOP_HOME"));
    if (otherArgs.length != 2) {
      System.err.println("Usage: WordCount <in> <out>");
      System.exit(2);
    }

    // create a job with name "wordcount"
    Job job = new Job(conf, "userflow");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(Map.class);
    job.setReducerClass(Reduce.class);
   
    // uncomment the following line to add the Combiner
    // job.setCombinerClass(Reduce.class);
    // set output key type   
    job.setOutputKeyClass(Text.class);
    // set output value type
    job.setOutputValueClass(IntWritable.class);
    
    //set reduce number
    job.setNumReduceTasks(1);
    //set the HDFS path of the input data
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    // set the HDFS path for the output
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    //Wait till job completion
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}

```

- 示例输入输出：
```
输入：用户手机号和流量，用空格分割
13500000000 10
13800000000 20
13500000000 20
13800000000 15
18700000000 20

输出：每个手机号的总流量
13500000000 30
13800000000 35
18700000000 20
```

### 分类（classfication）
- 问题定义：在数据文件中包含大量的记录，每条记录中包含了某个实体的若干属性，目标问题是在给定一个计算函数的情况下，将此计算函数计算后得到的结果相同的实体放在一起；
- 具体问题描述：输入数据为两列，分别是手机号和其访问的网址，目的是统计同一个网址有哪些手机号访问过；
- 解决方案：map阶段把网址、手机号分别作为key、value传入reduce，由reduce实现分类；
- 程序：

```
package wordcountTest;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.*;
import org.apache.hadoop.mapreduce.Counter;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

  public static class Map 
            extends Mapper<LongWritable, Text, Text, LongWritable>{

    private final static IntWritable one = new IntWritable(1); // type of output value
    private Text webPage = new Text();   // type of output key
    private LongWritable flow = new LongWritable();
      
    public void map(LongWritable key, Text value, Context context
                    ) throws IOException, InterruptedException {
    	String line = value.toString();
    	String [] array = line.split(" ");
    	if(array.length==2){
    		webPage.set(array[1]);
    		flow = new LongWritable(Long.parseLong(array[0]));
    		context.write(webPage,flow);
    	}/*else{
    		return;
    	}*/
    }
  }
  
  public static class Reduce
       extends Reducer<Text, LongWritable, Text, LongWritable> {

    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<LongWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {

      for (LongWritable val : values) {
    	  context.write(key, val);
      }
    }
  }

  // Driver program
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration(); 
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs(); // get all args
    System.out.println(System.getenv("HADOOP_HOME"));
    if (otherArgs.length != 2) {
      System.err.println("Usage: WordCount <in> <out>");
      System.exit(2);
    }

    // create a job with name "wordcount"
    Job job = new Job(conf, "userflow");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(Map.class);
    job.setReducerClass(Reduce.class);
   
    // uncomment the following line to add the Combiner
    // job.setCombinerClass(Reduce.class);
    // set output key type  

    job.setOutputKeyClass(Text.class);
    // set output value type
    job.setOutputValueClass(LongWritable.class);
    
    //set reduce number
    job.setNumReduceTasks(3);
    //set the HDFS path of the input data
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    // set the HDFS path for the output
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    //Wait till job completion
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}

```

- 示例输入输出：
```
输入：手机号和网址，用空格分割
18500000001 sina.com
18500000002 souhu.com
18500000003 sina.com
18500000004 sina.com
18500000005 souhu.com
18500000006 github.com
18500000007 sina.com
18500000008 github.com

输出：每个网址对应的访问过它的手机号
github.com 18500000006
github.com 18500000008
sina.com 18500000001
sina.com 18500000003
sina.com 18500000004
sina.com 18500000007
souhu.com 18500000002
souhu.com 18500000005
```

- 说明：本程序并不能实现将每一个网址放入单独的文件输出，这是因为在“shuffle”过程中，计算出的partitionID相同的key被分到一个reduce中，然而，不同的key的partitionID可能相同，即同一个key肯定被分到同一个reduce，但是同一个reduce中可能有不同的key，所以上述算法不能实现完全分类。关于shuffle请参考我的另一篇博客[Mapreduce过程详解](http://changsiyuan.github.io/hadoop/2015/04/01/mapreduce/)


