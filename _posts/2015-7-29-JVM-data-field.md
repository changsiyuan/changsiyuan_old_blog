---
layout : post
title : JVM运行时数据区
category : JVM
tagline : "author: ChangSiyuan"
tags : [JVM]
---
{% include JB/setup %}

## JVM运行时数据区的理解

- 运行时数据区面向线程，而不面向类或方法或对象；
- 线程共享：方法区、堆内存（heap）,线程私有：虚拟机栈（栈内存stack）、本地方法栈、程序计数器；
- 程序计数器：存放线程执行位置（main函数也是一个线程）；
- 虚拟机栈（栈内存）：生命周期和对应的线程相同，存放该线程的局部变量、对象的引用；方法中可能嵌套调用别的方法，这些方法的变量以”先进后出、后进先出“的方式在栈内存中存储，所以内层方法无法访问外层方法的变量（他们在栈底），执行外层方法时内层方法的变量已经弹出栈消失了，所以外层方法也不会访问到内层方法的变量；一个方法的执行过程，就是这个方法对于帧栈的入栈出栈过程；
- 本地方法栈：作用和虚拟机栈完全相同；虚拟机栈对应普通的java方法，本地方法栈对应native方法；native方法是一个java调用非java代码的接口；
- 方法区：JVM内存管理中最大一块；存储常量（final修饰）、类变量/成员变量（static修饰）、类信息（每个类的访问控制符、修饰符、继承关系等）；
- 堆内存：JVM启动时创建，存放对象本身，不存放对对象的引用（引用存在栈内存）；
- JVM垃圾收集特指收集堆内存中的失去引用的对象的存储空间；栈内存存储空间不予收集；如果一个对象长期不失去引用、但在程序中已经没有用，那么系统不会进行垃圾收集，可能造成内存泄露/内存溢出；