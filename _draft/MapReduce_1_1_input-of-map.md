#Map的输入：以TextInputFormat为例
***
这里解答第一个问题，文本怎么变成(K,V)的。
一个大体的流程就是:
* 首先把一个文件分成几个Split，每个Split交给一个MapTask去处理。
* 那么什么是Split？如何划分的Split?如何处理这个Split呢？Split和单个文件内容上有什么关系，使用上有什么区别？
* 以TextInputFormat为例，每个Split被最终变成了一些(K,V)，每个(K,V)代表一行，K是该行开始在文件中第几个字节，V是该行的内容。
* 那么问题来了，Split是直接按照大小分的，而每行则是按照'\n'(如果是UNIX风格，当然还有其他的划分方法)来划分的，这两者之间的分界线显然不是相同的，如何处理这两者的不同呢？

***
###类之间的关系以及功能
***

![TextInputFormat](/_image/1.TextInputFormat.png)

* 可以看出来InputFormat就是一个抽象的基类。
* FileInputFormat实现了一个重要的方法getSplit()。这个方法将一个文件进行分割成不同的Split。
* 而TextInputFormat则仅仅是使用createRecordReader创建了一个对象，该对象是负责把Split变成生成(K,V)的。可以知道这个对象是LineRecordReader类
* 不过，首先看一下LineRecordReader中使用的一个辅助类LineReader
* LineReader的作用就是从一个文件流中读取一行，就是方法readLine()。这里呢，不对输入做任何的假设，就是当作一个完整的文件读取出一行。
* 处理行的分界线和Split的分界线不同是在LineRecordReader中处理的。

***
###FileInputFormat
***
这里重点分析getSplit()

***
###TextIputFormat
***
这里还是很简单的就是两个方法
```
 public RecordReader<LongWritable, Text> 
    createRecordReader(InputSplit split,
                       TaskAttemptContext context) {
    return new LineRecordReader();
  }
  protected boolean isSplitable(JobContext context, Path file) {
    CompressionCodec codec = 
      new CompressionCodecFactory(context.getConfiguration()).getCodec(file);
    return codec == null;
  }
```
* createRecordReader就是创建了一个新的LineRecordReader对象。
* 这里
* isSplitable所名是否可以Split，毕竟有的是不可以分割的。

***
###LineReader
***
readLine

***
###LineRecordReader
***
initialize()

```
if (skipFirstLine) {  // skip first line and re-establish "start".
  istart += in.readLine(new Text(), 0,
  (int)Math.min((long)Integer.MAX_VALUE, end - start));
}
```
nextKeyValue()

```
while (pos < end) {
      newSize = in.readLine(value, maxLineLength,
                            Math.max((int)Math.min(Integer.MAX_VALUE, end-pos),
                            maxLineLength));
      if (newSize == 0) {
          break;
      }
      pos += newSize;
      if (newSize < maxLineLength) {
          break;
      }
}
```

* 读取的停止条件可以看出，每次都是读取完整的一行的，即使是超过了Split的边界(其实这个边界就是相当于是一个约定而已，可以做稍微的调整)。
* 那么，假设有邻近的两块，第二块的开头已经被读过了，那么怎么保证不重复读取呢？第二块跳过第一个'\n'之前的内容。
* 但是如果是第一块呢，不应该跳过，那么就不跳。结果就是initialize()中的判断是否跳过第一个'\n'之前的内容。
* 注意，第一个'\n'之前的内容和第一行也不同，一行的内容应该是完整的，但是第二块中的第一行可能一部分在第一块的末尾，一部分在第二块的开头.
* initialize()和getKeyValue()对不同Map处理的内容做了微微调整，基本上保证了每个Split由一个Map处理的语义。

***
#实现自己的InputFormat
***
* 如果你不需要实现自己的Split，仅仅是想修改生成(K,V)的方法，那么只需要修改在createRecordReader中实现一个新的对象，并且实现这个类就可以了。比如说，每100字节作为一个(K,V)对之类的。
* 如果需要自定义Split的方法，可以重新实现getSplit方法和相应的对(K,V)边界和Split边界不同的处理
