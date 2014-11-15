#ReduceTask的流程
***
###一、ReduceTask中的流程
***
```
  public void run(JobConf job, final TaskUmbilicalProtocol umbilical)
    throws IOException, InterruptedException, ClassNotFoundException {
    if (isMapOrReduce()) {
      copyPhase = getProgress().addPhase("copy");
      sortPhase  = getProgress().addPhase("sort");
      reducePhase = getProgress().addPhase("reduce");
    }
    //...精简代码
    copyPhase.complete();                         // copy is already complete
    setPhase(TaskStatus.Phase.SORT);
    statusUpdate(umbilical);

    final FileSystem rfs = FileSystem.getLocal(job).getRaw();
    //非单机的情况下，使用的得到reduce的输入
    RawKeyValueIterator rIter = reduceCopier.createKVIterator(job, rfs, reporter);
        
    sortPhase.complete();                         // sort is complete
    setPhase(TaskStatus.Phase.REDUCE); 
    //..精简代码
      runNewReducer(job, umbilical, reporter, rIter, comparator, 
                    keyClass, valueClass);
  }
```
* MapTask中就只有MAP Phase
* ReduceTask集中了三个阶段
 * COPY Phase
 * SORT Phase
 * REDUCE Phase
***
###一、Reducer运行的核心逻辑
```java
  public void run(Context context) throws IOException, InterruptedException {
    setup(context);
    while (context.nextKey()) {
      reduce(context.getCurrentKey(), context.getValues(), context);
    }
    cleanup(context);
  }
```

###二、默认reduce方法
```java
  protected void reduce(KEYIN key, Iterable<VALUEIN> values, Context context
                        ) throws IOException, InterruptedException {
    for(VALUEIN value: values) {
      context.write((KEYOUT) key, (VALUEOUT) value);
    }
  }
```
###三、Reducer的实例化
```java
    //runNewReducer()部分代码
    //初始化代码
    org.apache.hadoop.mapreduce.Reducer<INKEY,INVALUE,OUTKEY,OUTVALUE> reducer =
      (org.apache.hadoop.mapreduce.Reducer<INKEY,INVALUE,OUTKEY,OUTVALUE>)
        ReflectionUtils.newInstance(taskContext.getReducerClass(), job);
    ...
    reducer.run(reducerContext);
```
###四、输入的实例化
```
    final RawKeyValueIterator rawIter = rIter;
    rIter = new RawKeyValueIterator() {
      public void close() throws IOException {
        rawIter.close();
      }
      public DataInputBuffer getKey() throws IOException {
        return rawIter.getKey();
      }
      public Progress getProgress() {
        return rawIter.getProgress();
      }
      public DataInputBuffer getValue() throws IOException {
        return rawIter.getValue();
      }
      public boolean next() throws IOException {
        boolean ret = rawIter.next();
        reducePhase.set(rawIter.getProgress().get());
        reporter.progress();
        return ret;
      }
    };
```
###五、输出的实例化
```
     org.apache.hadoop.mapreduce.RecordWriter<OUTKEY,OUTVALUE> trackedRW = 
       new NewTrackingRecordWriter<OUTKEY, OUTVALUE>(reduceOutputCounter,
         job, reporter, taskContext);
```
