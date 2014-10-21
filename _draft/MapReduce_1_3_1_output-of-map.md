#map的输出结果
***
###数据在map中的流动
***
![output-of-map](/_image/2.output-of-map.png)
* Partition就是为了把输出的数据分给ReduceTask，就是再次切西瓜
* Serialize
* 把数据写入内存的缓冲区

**以上是mainThread做的事情，获取(K,V)->map->写入缓冲区一气呵成**

**下面的就是spillTread做的事情了**

* 如果mainThread发现内存缓冲区到某个临界条件了，那么就唤醒spillThread
* spillThread把内存中的数据sortAndSpill到硬盘上,每次spill都会生成一个文件（溢写文件）。
* 如果有设置combiner,那么先执行combine，在写入到硬盘上。

**如果所有数据都已经完成处理的时候，spillThread完成了使命，就应该退出**

* mainThread调用sortAndSpill()后，mergePart()会将多个溢写文件变成一个文件。

***
###Partition
***
```
public void write(K key, V value) throws IOException, InterruptedException {
    collector.collect(key, value,
                      partitioner.getPartition(key, value, partitions));
}
```
* 上次说到，存入的数据是(key,value,partition)三个数据，那么partition是用来干什么的呢？
* 前面说过Split是为了分西瓜给MapTask吃，那么partition就是为了分西瓜给ReduceTask吃，这个数字就是标示这个(K,V)属于哪个ReduceTask.

####Partitioner的实例化

```
来自org.apache.hadoop.mapred.NewOutputCollector部分代码
 partitioner = (org.apache.hadoop.mapreduce.Partitioner<K,V>)
           ReflectionUtils.newInstance(jobContext.getPartitionerClass(), job);

来自org.apache.hadoop.mapreduce,JobContext
   public Class<? extends Partitioner<?,?>> getPartitionerClass() 
      throws ClassNotFoundException {
     return (Class<? extends Partitioner<?,?>>) 
      conf.getClass(PARTITIONER_CLASS_ATTR, HashPartitioner.class);
   }
```
* 默认的方法是HashPartition

***
###Serialize
***
***
###内存缓冲区
***
***
###Spill
***
**spillThread调用的是sortAndSpill()**
* 先使用的是QuickSort()对内存缓冲区中需要写硬盘上的这部分数据进行排序
* 然后使用MergeQueue写入硬盘。

####Sort的设计

####溢写文件的结构

####combiner

####Merge

* 由于Merge在Map和Reduce中均有使用，而且非常复杂，之后做单独分析。
* 在这里就知道他是可以把多个溢写的文件合并成一个文件
 * 之前的多个文件是先按照partition来排序，partition相同的则按照key来排序的
 * 新的文件是也是先按照partition来排序，partition相同的则按照key来排序的一个文件
