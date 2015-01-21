# 1.编程的观点
* 一个理想的mapreduce框架
* 流式处理框架

# 2.数据流
### (1)数据的存储位置
* 输入文件 map 缓冲区 硬盘 reduce机器 (硬盘或内存) reduce 输出文件

### (2)kv的变化
* kv,k,list<V>
* 还有数据的各种处理
* 排序，combine,merge,serialize

###3.调优
* map的数量由splits决定
* reduce的数量由partition决定
* merge存在造成了I/O瓶颈
* 一个TaskTracker可运行map的数量最大值，一个job可以运行map的数量最大值
* 一个TaskTracker可运行reduce的数量最大值，一个job可以运行reduce的数量最大值
* 如何减少I/O（压缩文件，内存缓冲），网络I/O(多备份)
* 充分利用CPU的核数，增大并发量
* 但是要降低单个CPU的使用率(不能到100%)
* 提高内存的使用率
* 均衡各个Task
* key过多而值过少
或者key过少，值过多
