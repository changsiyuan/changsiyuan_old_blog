***
#MapTask的数据处理流程
***
###一、Mapper运行的核心逻辑
（来自org.apache.hadoop.mapreduce.Mapper）
***

```
public void run(Context context) throws IOException, InterruptedException {
    setup(context);
    while (context.nextKeyValue()) {
      map(context.getCurrentKey(), context.getCurrentValue(), context);
    }
    cleanup(context);
}
```

这个做了几件事情
* 做了一些配置
* 不断的读取下一个<K,V>，并交给map来处理（这里其实就是RecordReader中实现的对应的getCurrentKey()和getCurrentValue()）

####以WordCount来举例，
* 类LineRead可以从流中读取一行，类LineRecordReader将这一行变成一个键值对。
* 之后如何获取这些键值对并且处理呢？  注意： map()只处理**一个**键值对
* 但是每个split中肯定不止一行，那么是如何不断的读取每一行，生成一个个的键值对来处理呢？
* 答案就是这里，Mapper在运行的时候不断的获得键值对，不断的交给map去处理。

***
##二、默认的map方法
（来自org.apache.hadoop.mapreduce.Mapper）
***

```
  protected void map(KEYIN key, VALUEIN value, 
                     Context context) throws IOException, InterruptedException {
    context.write((KEYOUT) key, (VALUEOUT) value);
  }
```
默认的方法就是什么都不做，输入和输出时一样的

***
##三、Mapper的实例化
（来自org.apache.hadoop.mapred.MapTask）
***
重点就runNewMapper方法，这方法的内容很多，但是做的事就是先做一些初始化的操作，然后启动mapper
  
  初始化的时候使用到了反射机制，反射机制具体是什么样子的这里就不解释了。只需知道，有些类是要在运行的时候才知道是什的类，然后使用这些类，反射机制就可以实现这个功能。可以在其中看到一些get**class方法，如果这些类没有设置，就使用默认，如果使用过了就使用设置的类。
  
  在构造mapperContext中，更要使用contextConstructor来得到一个新的实例。
  
  总之

 ```
//一些初始化操作
      mapper.run(mapperContext);
//清理工作
```

***
##四、输入的实例化
（来自org.apache.hadoop.mapred.MapTask）
***

输入的方法和输入的对象的实例化是分开的，这个感觉有点奇怪。输入的对象难道使用输入的方法如何来输入内容呢？

这里的差别来至于InputFormat实现的是将分为split，生成一个输入对象(到此为止，至于这个对象能够干什么，它不知道)

输入的对象则具体实现了将一个文件中的内容转换成了键值对

(1)输入的方法InputFormat

```
    // make the input format
    org.apache.hadoop.mapreduce.InputFormat<INKEY,INVALUE> inputFormat =
      (org.apache.hadoop.mapreduce.InputFormat<INKEY,INVALUE>)
        ReflectionUtils.newInstance(taskContext.getInputFormatClass(), job);
```

* 这个主要是如何从一个文件分为split，生成一个输入对象
* 可以看出来，调用了getInputFormatClass，这样可以获取到我们自己设置的输入类，
* 默认输入方法是TextInputFormat

(2)输入对象


```
    org.apache.hadoop.mapreduce.RecordReader<INKEY,INVALUE> input =
      new NewTrackingRecordReader<INKEY,INVALUE>
          (split, inputFormat, reporter, job, taskContext);
```

* NewTrackingRecordReader<INKEY,INVALUE>其实是对获得(K,V)的方法又进行了封装，增加了一些
记录。
* 这个输入对象才能够读取文本，生成键值对，提供获取key值，value值，读取下一个键值对的方法

***
##五、输出的实例
（来自org.apache.hadoop.mapred.MapTask）
***

(1).输出的对象RecordWriter  out

```
       // get an output object
      if (job.getNumReduceTasks() == 0) {
         output =
           new NewDirectOutputCollector(taskContext, job, umbilical, reporter);
      } else {
        output = new NewOutputCollector(taskContext, job, umbilical, reporter);
      }
```

(2).NewOutputCollector中的write()

可以从map的运行中看出，调用的输出对象context的write方法,其实就是out的write方法

```
  public void write(K key, V value) throws IOException, InterruptedException {
      collector.collect(key, value,
                        partitioner.getPartition(key, value, partitions));
    }
```

其中

```
MapOutputCollector<K,V> collector;
collector = new MapOutputBuffer<K,V>(umbilical, job, reporter);
```

这里可以知道了最终是使用的MapOutputBuffer中的collect()，直接写入了缓存中

之后，我们就要开始看MapOutputBuffer是如何处理这些数据的。
