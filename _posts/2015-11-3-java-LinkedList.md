---
layout : post
title : Java LinkedList的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- Java中，LinkedList是重要的数据结构；
- LinkedList用链表实现，每个节点不仅保存本节点的数据，还保存下个节点的引用；
- 本文实现了一个简单的LinkedList；

### LinkedList具体实现
- Node类，定义节点

```
package linkedtest;

public class Node<T> {
	protected Node<T> next;
	protected T data;

	public Node(T data) {
		this.data = data;
	}

	public void display() {
		System.out.println(data + " ");
	}
}

```

- MyLinkedList 具体实现类

```
package linkedtest;

public class MyLinkedList<T> {
	protected Node<T> first; // 存储首元素
	protected Node<T> last; // 存储末尾元素
	protected int point = 0; // 存储当前指针
	protected int size = 0; // 存储链表大小

	// 构造器
	public MyLinkedList() {

	}

	// 清空链表
	public void clear() {
		first = null;
		last = null;
		point = 0;
		size = 0;
	}

	// 添加元素到末尾
	public void add(T data) {
		Node<T> node = new Node<T>(data);
		if (size == 0) {
			first = node;
			first.next = null;
			last = node;
			last.next = null;
			size++;
		} else {
			last.next = node;
			last = node;
			size++;
		}
	}

	// 添加元素到特定位置
	public void add(T data, int index) {
		Node<T> node = new Node<T>(data);
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point != index) {
			previous = current;
			current = current.next;
			point++;
		}
		previous.next = node;
		node.next = current;
		size++;
	}

	// 添加元素到list首部
	public void addFirst(T data) {
		Node<T> node = new Node<T>(data);
		node.next = first;
		first = node;
		size++;
	}

	// 是否包含元素
	public boolean contains(T data) {
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point < size) {
			if (current.data.equals(data)) {
				return true;
			}
			previous = current;
			current = current.next;
			point++;
		}
		return false;
	}

	// 获取特定位置元素
	public T get(int index) {
		if (index >= size) {
			return null;
		}

		point = 0;
		Node<T> previous = first;
		Node<T> current = first;

		while (point != index) {
			previous = current;
			current = current.next;
			point++;
		}
		return current.data;
	}

	// 获取首元素
	public T getFirst() {
		return first.data;
	}

	// 获取尾元素
	public T getLast() {
		return last.data;
	}

	// 获取元素位置
	public int indexOf(T data) {
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point < size) {
			if (current.data.equals(data)) {
				return point;
			}
			previous = current;
			current = current.next;
			point++;
		}
		return -1;
	}

	// 反向获取元素位置
	public int lastIndexOf(T data) {
		int size1 = size;
		int point1 = 0;
		while (size1 > 0) {
			Node<T> previous = first;
			Node<T> current = first;
			while (point1 < size1 - 1) {
				previous = current;
				current = current.next;
				point1++;
			}
			if (current.data.equals(data)) {
				return size1 - 1;
			} else {
				size1--;
				point1 = 0;
			}
		}
		return -1;
	}

	// 删除特定位置元素
	public void remove(int index) {
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point < size) {
			if (point == index) {
				previous.next = current.next;
				break;
			}
			previous = current;
			current = current.next;
			point++;
		}
		size--;
	}

	// 删除特定元素
	public void remove(T data) {
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point < size) {
			if (current.data.equals(data)) {
				previous.next = current.next;
				break;
			}
			previous = current;
			current = current.next;
			point++;
		}
		size--;
	}

	// 删除第一个元素
	public void removeFirst() {
		first = first.next;
		size--;
	}

	// 删除最后一个元素
	public void removeLast() {
		point = 0;
		Node<T> previous = first;
		Node<T> current = first;
		while (point < size) {
			previous = current;
			current = current.next;
			if (point == size - 1) {
				last = previous;
				last.next = null;
				break;
			}
			point++;
		}
		size--;
	}

	// 是否为空
	public boolean isEmpty() {
		return size == 0;
	}
	
	//返回list大小
	public int size(){
		return size;
	}
	
	//遍历输出list
	public void displayAll(){
		point = 0;
		Node<T> node = first;
		while(point<size){
			node.display();
			node = node.next;
			point++;
		}
	}
}

```

- TestMyLinkedList 测试类

```
package linkedtest;

public class TestMyLinkedList {

	public static void main(String[] args) {
		MyLinkedList<String> mlist = new MyLinkedList<String>();
		
		mlist.add("111");
		mlist.add("222");
		mlist.displayAll();
		System.out.println("------------");
		
		mlist.clear();
		mlist.add("aaa");
		mlist.add("bbb");
		mlist.add("ddd");
		mlist.add("ccc");
		mlist.add("aaa");
		mlist.add("eee");
		mlist.displayAll();
		System.out.println("------------");
		
		mlist.add("sss", 3);
		mlist.displayAll();
		System.out.println("------------");
		
		mlist.addFirst("000");
		mlist.displayAll();
		System.out.println(mlist.size());  //8
		System.out.println("------------");
		
		System.out.println(mlist.contains("bbb"));  //true
		System.out.println(mlist.get(0));  //000
		System.out.println(mlist.get(1));  //aaa
		System.out.println(mlist.get(5));  //ccc
		System.out.println(mlist.get(8));  //null
		System.out.println(mlist.getFirst());  //000
		System.out.println(mlist.getLast());  //eee
		System.out.println(mlist.indexOf("sss"));  //4
		System.out.println(mlist.indexOf("aaa"));  //1
		System.out.println(mlist.lastIndexOf("aaa"));  //6
		System.out.println("------------");
		
		mlist.remove(2);
		System.out.println(mlist.size());
		mlist.remove("ddd");
		System.out.println(mlist.size());
		mlist.displayAll();
		System.out.println("------------");
		
		mlist.removeFirst();
		mlist.removeLast();
		mlist.displayAll();
		System.out.println("------------");
		
		System.out.println(mlist.isEmpty());  //false
		System.out.println(mlist.size());  //4
	}

}

```
