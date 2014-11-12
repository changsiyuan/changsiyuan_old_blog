一、为什么要排序
对partition排序
对key排序
为了最后将value合并
二、区分
sort 函数，sort的效果，sort phase
sort
spill 和merge的关系

sort函数的设计框架
三、出现排序的地方
sort
spill
merge的关系

sort phase
sort操作
sort效果
merge实际效果是排序
merge中的writeFile也是写入硬盘
spill是指超出内存的临界条件写入硬盘
