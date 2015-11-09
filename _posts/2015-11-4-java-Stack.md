---
layout : post
title : Java Stack的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- Stack是java中常用的数据结构；
- 栈遵循先进后出，后进先出；
- 本文实现了一个简单的栈；

### 栈的实现
- MyStack实现了栈的大部分方法

```
package test6;

public class MyStack<T> {
	public static final int DEFAULT_INITIAL_CAPACITY = 16;
	public int size = 0; // 栈大小
	protected T[] stack = (T[]) new Object[DEFAULT_INITIAL_CAPACITY];

	// 清空栈
	public void clear() {
		stack = (T[]) new Object[DEFAULT_INITIAL_CAPACITY];
		size = 0;
	}

	// 判断容量
	public void testCapacity() {
		if (size >= stack.length) {
			T[] stack1 = (T[]) new Object[stack.length * 2];
			System.arraycopy(stack, 0, stack1, 0, stack.length);
			stack = stack1;
		}
	}

	// 判断是否为空
	public boolean isEmpty() {
		return size == 0;
	}

	// Looks at the object at the top of this stack without removing it from the
	// stack
	public T peek() {
		return stack[size - 1];
	}

	// 压入数据
	public void push(T t) {
		testCapacity();
		stack[size] = t;
		size++;
	}

	// 弹出数据
	public T pop() {
		T t = stack[size - 1];
		size--;
		return t;
	}
	
	//Returns the 1-based position where an object is on this stack
	public int search(T t){
		for(int i =size-1; i>=0; i--){
			if(stack[i].equals(t)){
				return size-i;
			}
		}
		return -1;
	}
	
	//获取容量
	public int size(){
		return size;
	}
}

```

- TestMyStack测试类

```
package test6;

public class TestMyStack {

	public static void main(String[] args) {
		MyStack<String> s = new MyStack<String>();
		s.push("111");
		s.push("222");
		System.out.println(s.size());  //2
		s.clear();
		System.out.println(s.size());  //0
		System.out.println("----------");
		
		s.push("aaa");
		s.push("bbb");
		s.push("ccc");
		s.push("ddd");
		System.out.println(s.size());  //4
		System.out.println(s.isEmpty());  //false
		System.out.println(s.search("ccc"));  //2
		System.out.println(s.peek());  //ddd
		System.out.println("----------");
		
		s.pop();
		System.out.println(s.peek());  //ccc
	}

}

```
