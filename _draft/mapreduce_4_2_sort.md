#一、为什么要排序
***

* 对partition排序，是为了让同一个partition的数据聚集在一起，至于不同partition之间的顺序怎么样，无关紧要。
* 对key排序，是为了让同一个Key的数据聚集在一起，至于不同key之间的相对大小怎么样，同一个key中不同value的数据顺序怎么样，这都是无关紧要的。
* 而上述这些为了最后将value合并。只有同一个partition的数据聚集在一起，才能比较方便的发送给同一个reducer，同一个key的数据都聚集在一起，才能够让同一个key的value之后能够以List的形式出现。
* 在Merge操作依赖于同一个文件中同一个Partition的数据聚集在一起，同时也保证了通过Merge生成的文件同一个Partition的数据也是聚集在一起的
* (K,V)--->(K,List&lt;V>)都是依赖于同一个K的数据是聚集在一起的，只要连续的读取出来就可以把同一个key的所有value全部读取出来。

***
# 二、区分
***
* sort 函数，sort的效果，Sort Phase
 * sort函数是指对MapOutputBuffer中的缓冲区排序使用的sort，默认是快排
 * sort的效果是最后输出数据是排序的，除了上面的sort函数外，Merger类就是一个可以排序的迭代器
* Sort Phase是几个过程之一，是将Copy Phase之后的数据进行排序，
* Spill 和Merge的关系
 * Spill是指MapOutputBuffer使用到了一定的程度，需要写入硬盘，以减小内存压力
 * Spill每次生成一个文件，多次之后就有了很多的文件
 * Merge则是负责将这些文件合并成同一个文件
 * Spill 内存 ---> 硬盘
 * Merge 硬盘 ---> 硬盘

#排序的一些设计框架
* 通用排序的框架(内存中的对象)
 * 比较函数
 * 交换函数
 * 例子sort函数
* 通用排序框架（同时处理硬盘和内存中的文件）
 * 边排序边迭代
 * 对一部分对象进行排序，写入临时文件
 * 将多个已经排序的文件再次排序
 * 例子：Merger类

***
#三、出现排序的地方
***

* Spill过程使用sort函数进行排序
* Merge
* Sort Phase
