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
```java
public void write(K key, V value) throws IOException, InterruptedException {
    collector.collect(key, value,
                      partitioner.getPartition(key, value, partitions));
}
```
* 上次说到，存入的数据是(key,value,partition)三个数据，那么partition是用来干什么的呢？
* 前面说过Split是为了分西瓜给MapTask吃，那么partition就是为了分西瓜给ReduceTask吃，这个数字就是标示这个(K,V)属于哪个ReduceTask.

####Partitioner的实例化

```java
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

* 环形:kvnetx=(kvindex+1)%kvoffsets.length
* 这是一个两级索引(kvoffsets和kvindices)的环形缓冲区。
* 这个是看缓冲区不同层次之间的关系，那么每层又是如何使用的呢？

####如何使用kvoffsets

![kvoffsets](/_image/3.1.kvoffsets.png)

* 分成了几个部分，空闲、正在Spill、将要Spill。
* 这样同一个缓冲区，可以安全的由多个线程来同时操作。
* 当然kvoffsets中仅仅是写入了索引，实际的内容是在kvbuffer中的。

####写入kvbuffer的一般过程（四个步骤）

![kvbuffer1](/_image/3.2.kvbuffer1.png)
![kvbuffer2](/_image/3.3.kvbuffer2.png)

####写入kvbuffer的异常情况

* 排序时要求key是连续存放的，所以在写入Key之后，要检测是否连续，不连续就要进行相应的操作来保证连续。
* reset()就是用来保证key是连续的。 

![kvbuffer3](/_image/3.4.kvbuffer3.png)

###关于缓冲区的设计
* 这里需要满足的两个目标是不定长的数据和排序
* 对于不定长的数据来讲，一般的存储方法就是使用索引(起始位置,长度)
* 就是说(startOfPartition,lengthOfPartition,startOfKey,lengthOfKey,startOfValue,lengthOfValue)
* 如果排序的话，将这个作为单位来移动。
* MapOutputBuffer实现的索引是kvindices，具体的值存储在kvbuffer中。
* 但是kvindices没有长度，只有起始位置，那么如何读取数据呢？

#####Key的读取

```java
      public DataInputBuffer getKey() throws IOException {
        final int kvoff = kvoffsets[current % kvoffsets.length];
        keybuf.reset(kvbuffer, kvindices[kvoff + KEYSTART],
                     kvindices[kvoff + VALSTART] - kvindices[kvoff + KEYSTART]);
        return keybuf;
      }
```
* KV是连续存放在一起的。
* K本身的连续性在写入的时候已经保证
* 那么V的起始地址和K的起始地址之间就是key的值。

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
* KV是连续存放在一起的，那么就是KVKVKVKV这个样子的
* 就是说V的起始地址和下一个K的起始地址之间的数据就是V的值
* 但是缓冲区是环形的，所以V可能不是连续存放的
* InMemValBytes 这个类就是为了解决不连续这个问题存在的。

####分析
* 这个实现的优点就是可以不再存储KV的长度。
* 缺点呢，不是很明显。这种方法暗含着一个假设，就是索引的位置顺序和存储数据的位置顺序是一致的，
* 也就是说kvindices第一个索引，对应于kvbuffer第一个KV，
* kvindices第二个索引，对应于kvbuffer第二个KV。
* 由此产生的后果就是不可以对索引kvindices排序。
* 一旦对索引kvindices排序，那么key的值仍然是可以正确读出的，但是value的值的读取依赖于下一个索引中key的起始地址（排序之后，下一个就和写入的时候的不一样了），则不能正确读取。
* 那么再建立一层索引kvoffsets，来进行排序。

***
###Spill
***
* spillThread调用的是sortAndSpill()
* sort就是先使用的是QuickSort()对内存缓冲区中需要写硬盘上的这部分数据进行排序
* 然后使用Writer写入硬盘。

####Sort时从内存中读取数据

```
  final int kvoff = kvoffsets[spindex % kvoffsets.length];
  getVBytesForOffset(kvoff, value);
  key.reset(kvbuffer, kvindices[kvoff + KEYSTART],
             (kvindices[kvoff + VALSTART] - 
                kvindices[kvoff + KEYSTART]));
```

####Sort

```java
排序使用的是QuickSort
      sorter = ReflectionUtils.newInstance(
                  job.getClass("map.sort.class", QuickSort.class, IndexedSorter.class), job);
比较函数
    public int compare(int i, int j) {
      final int ii = kvoffsets[i % kvoffsets.length];
      final int ij = kvoffsets[j % kvoffsets.length];
      // sort by partition
      if (kvindices[ii + PARTITION] != kvindices[ij + PARTITION]) {
        return kvindices[ii + PARTITION] - kvindices[ij + PARTITION];
      }
      // sort by key
      return comparator.compare(kvbuffer,
          kvindices[ii + KEYSTART],
          kvindices[ii + VALSTART] - kvindices[ii + KEYSTART],
          kvbuffer,
          kvindices[ij + KEYSTART],
          kvindices[ij + VALSTART] - kvindices[ij + KEYSTART]);
    }
交换函数
    public void swap(int i, int j) {
      i %= kvoffsets.length;
      j %= kvoffsets.length;
      int tmp = kvoffsets[i];
      kvoffsets[i] = kvoffsets[j];
      kvoffsets[j] = tmp;
    }
```
就一个排序过程来讲，起始就是需要两个操作，比较大小，和交换位置。

这里排序的设计是相当精彩的。

####Writer写入硬盘

IFile类

####溢写文件的结构

![spillfile](/_image/3.5.spill.png)

####combiner
```java
            if (combinerRunner == null) {
              // spill directly
              DataInputBuffer key = new DataInputBuffer();
              while (spindex < endPosition &&
                  kvindices[kvoffsets[spindex % kvoffsets.length]
                            + PARTITION] == i) {
                final int kvoff = kvoffsets[spindex % kvoffsets.length];
                getVBytesForOffset(kvoff, value);
                key.reset(kvbuffer, kvindices[kvoff + KEYSTART],
                          (kvindices[kvoff + VALSTART] - 
                           kvindices[kvoff + KEYSTART]));
                writer.append(key, value);
                ++spindex;
              }
            } else {
              int spstart = spindex;
              while (spindex < endPosition &&
                  kvindices[kvoffsets[spindex % kvoffsets.length]
                            + PARTITION] == i) {
                ++spindex;
              }
              // Note: we would like to avoid the combiner if we've fewer
              // than some threshold of records for a partition
              if (spstart != spindex) {
                combineCollector.setWriter(writer);
                RawKeyValueIterator kvIter =
                  new MRResultIterator(spstart, spindex);
                combinerRunner.combine(kvIter, combineCollector);
              }
            }
```

* MRResultIterator类将缓冲区的读取变成了KV对的读取
* combiner的处理就是在spill写入硬盘之前。
* combiner就可以从缓冲区中把内容读取出来，进行处理，写入文件。

***
###Merge
***

* 由于Merge在Map和Reduce中均有使用，而且非常复杂，之后做单独分析。
* 在这里就知道他是可以把多个溢写的文件合并成一个文件
 * 之前的多个文件是先按照partition来排序，partition相同的则按照key来排序的
 * 新的文件是也是先按照partition来排序，partition相同的则按照key来排序的一个文件
 * 也就是说merge包含由排序和合并文件两个功能。
