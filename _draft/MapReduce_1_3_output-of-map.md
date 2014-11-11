#map的输出结果
***
###数据在map中的流动
***
![output-of-map](/_image/2.output-of-map.png)
* Partition就是为了把输出的数据分给ReduceTask，就是再次切西瓜
* Serialize
* 把数据写入内存的缓冲区

**以上是mainThread做的事情，获取(K,V)->map->写入缓冲区,在这之中又夹杂了一些小的操作比如Partition什么的**

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
####整体看MapOutputBuffer

![overview](/_image/3.0.MapOutputBuffer.png)

这是一个两级索引(kvoffsets和kvindices)的环形缓冲区。
这个是看缓冲区不同层次之间的关系，那么每层又是如何使用的呢？

####如何使用kvoffsets

![kvoffsets](/_image/3.1.kvoffsets.png)

分成了几个部分，空闲、正在Spill、将要Spill。
这样同一个缓冲区，可以安全的由多个线程来同时操作。
当然kvoffsets中仅仅是写入了索引，实际的内容是在kvbuffer中的。

####正常写入kvbuffer的四个步骤

![kvbuffer1](/_image/3.2.kvbuffer1.png)
![kvbuffer2](/_image/3.3.kvbuffer2.png)

####异常写入kvbuffer

![kvbuffer3](/_image/3.4.kvbuffer3.png)

####为什么要有两级索引

关于缓冲区的设计（先不考虑）
这里需要满足的两个目标是不定长的数据和排序
对于不定长的数据来讲，一般的存储方法就是使用索引(起始位置,长度)
就是说(startOfPartition,lengthOfPartition,startOfKey,lengthOfKey,startOfValue,lengthOfValue)
如果排序的话，将这个作为单位来移动。

MapOutputBuffer实现的索引是kvindices，具体的值存储在kvbuffer中。
但是kvindices没有长度，只有起始位置，那么如何读取数据呢？

#####Key的读取

```java
      public DataInputBuffer getKey() throws IOException {
        final int kvoff = kvoffsets[current % kvoffsets.length];
        keybuf.reset(kvbuffer, kvindices[kvoff + KEYSTART],
                     kvindices[kvoff + VALSTART] - kvindices[kvoff + KEYSTART]);
        return keybuf;
      }
```

#####Value的读取

```java
    public DataInputBuffer getValue() throws IOException {
      getVBytesForOffset(kvoffsets[current % kvoffsets.length], vbytes);
      return vbytes;
    }
    private void getVBytesForOffset(int kvoff, InMemValBytes vbytes) {
      final int nextindex = (kvoff / ACCTSIZE ==
                            (kvend - 1 + kvoffsets.length) % kvoffsets.length)
        ? bufend
        : kvindices[(kvoff + ACCTSIZE + KEYSTART) % kvindices.length];
      int vallen = (nextindex >= kvindices[kvoff + VALSTART])
        ? nextindex - kvindices[kvoff + VALSTART]
        : (bufvoid - kvindices[kvoff + VALSTART]) + nextindex;
      vbytes.reset(kvbuffer, kvindices[kvoff + VALSTART], vallen);
    }
```

这个实现的优点就是可以不再存储KV的长度。

缺点呢，不是很明显。这种方法暗含着一个假设，就是索引的位置顺序和存储数据的位置顺序是一致的，也就是说kvindices第一个索引，对应于kvbuffer第一个KV，

kvindices第二个索引，对应于kvbuffer第二个KV。

由此产生的后果就是不可以对索引kvindices排序。

一旦对索引kvindices排序，那么key的值仍然是可以正确读出的，但是value的值的读取依赖于下一个索引中key的起始地址（排序之后，下一个就和写入的时候的不一样了），则不能正确读取。

那么再建立一层索引kvoffsets，来进行排序。

***
###Spill
***
* spillThread调用的是sortAndSpill()
* sort就是先使用的是QuickSort()对内存缓冲区中需要写硬盘上的这部分数据进行排序
* 然后使用Writer写入硬盘。

####sort时从内存中读取数据

####Sort的比较函数

####Writer写入硬盘

IFile类

####溢写文件的结构

![spillfile](/_image/3.5.spill.png)

####combiner

***
###Merge
***

* 由于Merge在Map和Reduce中均有使用，而且非常复杂，之后做单独分析。
* 在这里就知道他是可以把多个溢写的文件合并成一个文件
 * 之前的多个文件是先按照partition来排序，partition相同的则按照key来排序的
 * 新的文件是也是先按照partition来排序，partition相同的则按照key来排序的一个文件
 * 也就是说merge包含由排序和合并文件两个功能。
