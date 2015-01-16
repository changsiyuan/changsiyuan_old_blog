KV系统的设计
文件的来源，文件的组织形式，排序

1.map的输入

从硬盘文件变成KV，同时要兼顾Partition

2.map的输出缓冲在内存中的时候，可能会使用combine

所以要把内存缓冲区封装成KV

3.Spill之后的（多个）溢写文件IFile，多个已经排序的文件，封装成新的可排序的KV

并且以KV的形式写入硬盘

4.SORT Phase将内存和硬盘中都有的数据封装成KV

5.将MapOutput对象的内存文件封装成KV

6.(K,V)--->(K,List<V>)


