---
layout : post
title : Java 队列的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- 队列是java中重要的数据结构；
- 本文用数组实现了简单的单端队列；

### 单端队列的实现
- MyQueue类实现了队列的大部分方法

```
package queuetest;

public class MyQueue<T> {
	public int size = 0; // 当前队列长度
	public int maxSize = 8; // 队列容量，默认8

	protected T[] queue = (T[]) new Object[maxSize]; // 队列用数组实现

	// 构造器
	public MyQueue() {

	}

	// 构造器
	public MyQueue(int maxSize) {
		this.maxSize = maxSize;
	}

	// 清空队列
	public void clear() {
		queue = (T[]) new Object[maxSize];
		size = 0;
	}

	// add,如果队列已满，则抛出异常
	public void add(T t) throws Exception {
		if (size == maxSize) {
			throw new Exception("队列容量已满.");
		} else {
			queue[size] = t;
			size++;
		}
	}

	// offer，添加一个元素并返回true，如果队列已满，则返回false
	public boolean offer(T t) {
		if (size < maxSize) {
			queue[size] = t;
			size++;
			return true;
		}
		return false;
	}

	// element,返回队列头部的元素,如果队列为空,则抛出异常
	public T element() throws Exception {
		if (size == 0) {
			throw new Exception("队列为空！");
		} else {
			return queue[0];
		}
	}

	// peek,返回队列头部的元素,如果队列为空，则返回null
	public T peek() {
		if (size == 0) {
			return null;
		} else {
			return queue[0];
		}
	}

	// remove,移除并返回队列头部的元素,如果队列为空则抛出异常
	public T remove() throws Exception {
		if (size != 0) {
			T t = queue[0];
			for (int i = 0; i < size - 1; i++) {
				queue[i] = queue[i + 1];
			}
			queue[size - 1] = null;
			size--;
			return t;
		} else {
			throw new Exception("队列为空！");
		}
	}

	// poll,移除并返问队列头部的元素,如果队列为空则返回null
	public T poll() {
		if (size != 0) {
			T t = queue[0];
			for (int i = 0; i < size - 1; i++) {
				queue[i] = queue[i + 1];
			}
			queue[size - 1] = null;
			size--;
			return t;
		} else {
			return null;
		}
	}

	// 当前队列长度
	public int size() {
		return size;
	}

	// 是否为空
	public boolean isEmpty() {
		return size == 0;
	}

	// 遍历队列元素
	public void display() {
		for (int i = 0; i < size; i++) {
			System.out.println(queue[i]);
		}
	}
}

```

- MyQueueTest 测试类

```
package queuetest;

public class MyQueueTest {

	public static void main(String[] args) throws Exception {
		MyQueue<String> mq = new MyQueue<String>(4);
		mq.add("111");
		mq.add("222");
		mq.add("333");
		mq.add("444");
		System.out.println(mq.size());  //4
//		mq.add("555");  //java.lang.Exception: 队列容量已满.
		System.out.println(mq.size());  //4
		System.out.println(mq.offer("666"));  //false
		System.out.println(mq.size());  //4
		System.out.println("-----------");
		
		System.out.println(mq.element());  //111
		System.out.println(mq.peek());  //111
		System.out.println("-----------");
		
		System.out.println(mq.isEmpty());  //false
		mq.display();  //111 222 333 444
		System.out.println("-----------");
		
		System.out.println(mq.remove());  //111
		System.out.println(mq.remove());  //222
		System.out.println(mq.remove());  //333
		System.out.println(mq.remove());  //444
		System.out.println(mq.size());    //0
//		System.out.println(mq.remove());  //java.lang.Exception: 队列为空！
		System.out.println(mq.poll());  //null
		System.out.println(mq.size());  //0
//		System.out.println(mq.element());  //java.lang.Exception: 队列为空！
		System.out.println(mq.peek());  //null
	}

}

```

