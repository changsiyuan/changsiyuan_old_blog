---
layout : post
title : Java 堆的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- 堆是节点含有可比较对象，并以如下方式组织的完全二叉树：每个节点含有的对象不小于(最大堆)/不大于(最小堆)其后代中的对象；
- 最大堆的根含有堆中最大的对象；
- 最大堆中任何节点的子树也是最大堆；
- 堆是java中重要的数据结构，堆排序算法就要用到堆；
- 本文实现了简单的堆；

### 堆的实现
- MyHeap类实现了堆的大部分方法

```
package heap;

import java.lang.reflect.Array;

public class MyHeap<T extends Comparable<T>> {
	public static final int INITIAL_SIZE = 16;
	Class<T> type;
	boolean flag = false;
	T[] heap;
	int size = 0; // 堆中元素数量

	// 构造器
	public MyHeap() {

	}

	// 构造器
	public MyHeap(Class<T> type) {
		heap = (T[]) Array.newInstance(type, INITIAL_SIZE);
		this.type = type;
	}

	// 清空堆
	public void clear() {
		heap = (T[]) Array.newInstance(type, INITIAL_SIZE);
		size = 0;
	}

	// 堆中元素数量
	public int size() {
		return size;
	}

	// 检查数组大小
	public void checkSize() {
		if (size > heap.length / 2) {
			T[] heap1 = (T[]) Array.newInstance(type, INITIAL_SIZE * 2);
			System.arraycopy(heap, 0, heap1, 0, heap.length);
			heap = heap1;
		}
	}

	// 求父节点数组下标
	public int getParent(int i) {
		return (i + 1) / 2 - 1;
	}

	// 求左子节点数组下标
	public int getLeftChild(int i) {
		int t = (i + 1) * 2 - 1;
		return t;
	}

	// 求右子节点数组下标
	public int getRightChild(int i) {
		int t = (i + 1) * 2;
		return t;
	}

	// 获取某元素下标
	public int getEle(T t) {
		for (int i = 0; i < size; i++) {
			if (heap[i].equals(t)) {
				flag = true;
				return i;
			}
		}
		return 0;
	}

	// 换位置
	public void swap(int i, int j) {
		T temp;
		temp = heap[i];
		heap[i] = heap[j];
		heap[j] = temp;
	}

	// 维护堆的性质(小顶堆)
	public void buildHeap(int i) {
		if (i > 0) {
			if (heap[i].compareTo(heap[getParent(i)]) < 0) {
				swap(i, getParent(i));
			}
			buildHeap(getParent(i));
		}
	}

	// 插入元素
	public void insert(T t) {
		checkSize();
		heap[size] = t;
		buildHeap(size);
		size++;
	}

	// 获取最小元素（堆顶元素）
	public T minEle() {
		return heap[0];
	}

	// 获取某个元素的父元素
	public T getParentEle(T t) {
		flag = false;
		int i = getEle(t);
		if (!flag) {
			return null;
		} else if (i == 0)
			return heap[0];
		else {
			return heap[getParent(i)];
		}
	}

	// 获取某元素左孩子
	public T getLeftChildEle(T t) {
		flag = false;
		int i = getEle(t);
		if (!flag) {
			return null;
		} else if (getLeftChild(i) >= size) {
			return null;
		} else {
			return heap[getLeftChild(i)];
		}
	}

	// 获取某元素的右孩子
	public T getRightChildEle(T t) {
		flag = false;
		int i = getEle(t);
		if (!flag) {
			return null;
		} else if (getRightChild(i) >= size) {
			return null;
		} else {
			return heap[getRightChild(i)];
		}
	}

	// 遍历堆（先序遍历）
	public void getPreOrder() {
		for (int i = 0; i < size; i++) {
			System.out.println(heap[i]);
		}
	}
}

```

- TestMyHeap为测试类

```
package heap;

public class TestMyHeap {

	public static void main(String[] args) {
		MyHeap<Integer> h = new MyHeap<Integer>(Integer.class); 
		h.insert(10);
		h.insert(11);
		h.insert(9);
		System.out.println(h.size());  //3 
		h.getPreOrder();  //9 11 10
		System.out.println("----------");
		
		h.clear();
		h.insert(10);
		h.insert(11);
		h.insert(8);
		h.insert(9);
		h.insert(10);
		h.insert(12);
		h.insert(6);
		h.insert(13);
		h.insert(4);
		h.insert(7);
		System.out.println(h.size());  //10
		System.out.println(h.minEle());  //4
		h.getPreOrder();  //4 6 8 9 7 12 10 13 11 10
		System.out.println("----------");
		
		System.out.println(h.getParentEle(4));  //4
		System.out.println(h.getParentEle(6));  //4
		System.out.println(h.getParentEle(12)); //8
		System.out.println(h.getParentEle(13)); //9
		System.out.println(h.getParentEle(14)); //null
		System.out.println(h.getLeftChildEle(4));  //6
		System.out.println(h.getLeftChildEle(13)); //null
		System.out.println(h.getLeftChildEle(7));  //10
		System.out.println(h.getLeftChildEle(14)); //null
		System.out.println(h.getRightChildEle(4));  //8
		System.out.println(h.getRightChildEle(8));  //10
		System.out.println(h.getRightChildEle(7));  //null
		System.out.println(h.getRightChildEle(11)); //null
	}

}

```

