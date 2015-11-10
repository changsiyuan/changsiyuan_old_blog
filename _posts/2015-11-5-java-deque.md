---
layout : post
title : Java 双端队列的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- 双端队列是java中重要的数据结构，有着广泛的应用；
- 双端队列一般用双向链表实现；
- 本文就用双向链表实现了简单的双端队列；

### Deque的实现
- Node类定义双端队列的节点

```
package deque;

public class Node<T> {
	public Node<T> before;
	public Node<T> after;
	public T data;

	// 构造器
	public Node() {

	}

	// 构造器
	public Node(T t) {
		this.data = t;
	}

	public Node<T> getBefore() {
		return before;
	}

	public void setBefore(Node<T> before) {
		this.before = before;
	}

	public Node<T> getAfter() {
		return after;
	}

	public void setAfter(Node<T> after) {
		this.after = after;
	}
	
	public void display() {
		System.out.println(data + " ");
	}
}

```

- Deque类实现了双端队列的大部分方法

```
package deque;

public class Deque<T> {
	public int maxSize = 8; // 默认队列容量为8
	public int size = 0; // 双端队列的长度
	public Node<T> first; // 首节点
	public Node<T> last; // 尾节点

	// 构造器
	public Deque() {

	}

	// 构造器
	public Deque(int maxSize) {
		this.maxSize = maxSize;
	}

	// 清空队列
	public void clear() {
		size = 0;
		first = null;
		last = null;
	}

	// 在首部添加
	public void addToFront(T t) throws Exception {
		Node<T> node = new Node<T>();
		node.data = t;
		if (size == 0) {
			first = node;
			last = node;
			size++;
		} else if (size < maxSize) {
			node.setAfter(first);
			first.setBefore(node);
			first = node;
			size++;
		} else {
			throw new Exception("队列已满！");
		}
	}

	// 在尾部添加
	public void addToBack(T t) throws Exception {
		Node<T> node = new Node<T>();
		node.data = t;
		if (size == 0) {
			first = node;
			last = node;
			size++;
		} else if (size < maxSize) {
			node.setBefore(last);
			last.setAfter(node);
			last = node;
			size++;
		} else {
			throw new Exception("队列已满！");
		}
	}

	// 从首部删除
	public void removeFront() throws Exception {
		if (size == 0) {
			throw new Exception("队列为空");
		} else if (size == 1) {
			clear();
		} else {
			first = first.getAfter();
			first.setBefore(null);
			size--;
		}
	}

	// 从尾部删除
	public void removeBack() throws Exception {
		if (size == 0) {
			throw new Exception("队列为空");
		} else if (size == 1) {
			clear();
		} else {
			last = last.getBefore();
			last.setAfter(null);
			size--;
		}
	}

	// 获取首部元素
	public T getFront() {
		if (size == 0) {
			return null;
		} else {
			return first.data;
		}
	}

	// 获取尾部元素
	public T getBack() {
		if (size == 0) {
			return null;
		} else {
			return last.data;
		}
	}

	// 判断是否为空
	public boolean isEmpty() {
		return size == 0;
	}
	
	//判断是否队列已满
	public boolean isFull(){
		return size==maxSize;
	}
	
	//获取队列长度
	public int size(){
		return size;
	}

	// 输出队列全部内容
	public void displayAll() {
		if (size != 0) {
			Node<T> node = first;
			while (node != null) {
				node.display();
				node = node.getAfter();
			}
		}
	}
}

```

- DuqueTest测试类

```
package deque;

public class DequeTest {

	public static void main(String[] args) throws Exception {
		Deque<String> d = new Deque<String>(8);
		d.addToFront("111");
		d.addToFront("222");
		d.addToBack("333");
		System.out.println(d.size());  //3
		d.displayAll();  //222 111 333
		System.out.println("----------");
		
		d.clear();
		System.out.println(d.size());  //0
		System.out.println("----------");
		
		d.addToFront("aaa");
		d.addToFront("bbb");
		d.addToFront("ccc");
		d.addToFront("ddd");
		d.addToBack("eee");
		d.addToBack("fff");
		System.out.println(d.size());  //6
		d.displayAll();  //ddd ccc bbb aaa eee fff  
		System.out.println("----------");
		
		d.removeFront();
		d.removeBack();
		d.removeBack();
		System.out.println(d.size());  //3 
		d.displayAll();  //ccc bbb aaa
		System.out.println("----------");
		
		System.out.println(d.getFront());  //ccc
		System.out.println(d.getBack());  //aaa
		System.out.println("----------");
		
		System.out.println(d.isEmpty());  //false
		System.out.println(d.isFull());   //false
	}

}

```


