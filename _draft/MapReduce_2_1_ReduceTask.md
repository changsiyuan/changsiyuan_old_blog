#ReduceTask的流程
***

ReduceTask reducer reduce之间的关系
* ReduceTask类似于OS中一个进程
* reducer仅仅是其中一个专门处理数据的对象,那么还需要输入数据和输出数据的对象
* reduce就是reducer如何处理数据的一个方法
* 但是ReduceTask不像MapTask那样简单，ReduceTask包含有三个阶段
 * COPY Phase
 * SORT Phase
 * REDUCE Phase

***
###ReduceTask中的流程
***

```java
  public void run(...){
    copyPhase.complete();// copy is already complete
    sortPhase.complete();// sort is complete
    runNewReducer(...);
  }
  void runNewReducer(... ) {
    //初始化输入、reducer、输出对象
    reducer.run(reducerContext);
  }

```
* MapTask中就只有MAP Phase
* ReduceTask集中了三个阶段
 * COPY Phase
 * SORT Phase
 * REDUCE Phase

***
###Reducer运行的核心逻辑
***
```java
    public void run(Context context) throws IOException, InterruptedException {
        setup(context);
        while (context.nextKey()) {
          reduce(context.getCurrentKey(), context.getValues(), context);
       }
        cleanup(context);
    }
    默认的reduce方法
    protected void reduce(KEYIN key, Iterable<VALUEIN> values, Context context
                          ) throws IOException, InterruptedException {
        for(VALUEIN value: values) {
          context.write((KEYOUT) key, (VALUEOUT) value);
        }
    }
```
#####流程如下：
* setup方法做了一些配置，默认是空。
* 不断的读取下一个&lt;K,V>，并交给reduce一个一个KV来处理
* 默认的reduce方法就是什么都不做，输入和输出时一样的
* 这里使用的getCurrentKey()和getCurrentValue()是对输入的封装。
* 这里使用的write方法是对输出对象的封装

#####以WordCount来举例
* 在COPY Phase和SORT Pahse时，map处理之后的数据已经收集好了
* 之后如何获取这些键值对并且处理呢？  注意： reduce()只处理**一个**键值对
* 但是每个map处理之后的数据中肯定非常多，那么是如何把处理所有的键值呢？
* 答案就是这里，Reducer在运行的时候不断的获得键值对，不断的交给reduce去处理。

***
###输入的实例化
***

```
    ReduceTask中的run方法
    RawKeyValueIterator rIter = reduceCopier.createKVIterator(job, rfs, reporter);
    这个是Reducer的输入，就是将收集到的map输出封装成KV

    之后在runNewReducer中又对该Iterator进行了一些封装。
```
***
###输出的实例化
***
```
     org.apache.hadoop.mapreduce.RecordWriter<OUTKEY,OUTVALUE> trackedRW = 
       new NewTrackingRecordWriter<OUTKEY, OUTVALUE>(reduceOutputCounter,
         job, reporter, taskContext);
```
* 这个起始就是对FileOutputFormat的封装
* FileOutputFormat和FileInputFormat比较类似，这里不再赘述
