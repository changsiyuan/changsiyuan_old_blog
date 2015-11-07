---
layout : post
title : Java ArrayList的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- Java中，ArrayList是重要的数据结构；
- ArrayList用数组实现；
- 本文实现了一个简单的ArrayList；

### 具体实现
- MyArrayList 完成大部分方法实现

```
package arraylisttest;

public class MyArrayList<T> {
	public static final int initialSize = 16;
	Object[] array = new Object[initialSize];
	T[] myArray = (T[]) array;

	int size = 0; // 数组长度

	// 构造器
	public MyArrayList() {

	}

	// 删除全部元素
	public void clear() {
		array = new Object[initialSize];
		myArray = (T[]) array;
		size = 0;
	}

	// 检测数组空间是否充足
	public void ensureCapacity() {
		if (size > (int) (myArray.length / 2)) {
			Object[] newArray = new Object[array.length * 2];
			T[] newMyArray = (T[]) newArray;
			System.arraycopy(array, 0, newMyArray, 0, myArray.length);
			myArray = newMyArray; // 新数组的引用赋值给原数组变量名，原数组的堆中的存储空间失去引用，交给垃圾回收处理
		}
	}

	// 在末尾增加元素
	public void add(T data) {
		ensureCapacity();
		myArray[size] = data;
		size++;
	}

	// 在特定位置增加元素
	public void add(T data, int index) {
		ensureCapacity();
		T temp = null;
		for (int i = index; i < myArray.length; i++) {
			temp = myArray[i];
			myArray[i] = data;
			data = temp;
		}
		size++;
	}

	// 检测是否存在元素
	public boolean contains(T data) {
		for (int i = 0; i < size; i++) {
			if (myArray[i].equals(data)) {
				return true;
			}
		}
		return false;
	}

	// 获取特定元素
	public T get(int index) {
		return myArray[index];
	}

	// 获取特定元素的索引值
	public int indexOf(T data) {
		for (int i = 0; i < size; i++) {
			if (myArray[i].equals(data)) {
				return i;
			}
		}
		return -1;
	}
	
	//获取元素最后一次出现的索引值
	public int lastIndexOf(T data){
		int index = 0;
		for(int i = 0; i < size; i++){
			if (myArray[i].equals(data)) {
				index = i;
			}
		}
		return index;
	}

	// 判断是否为空
	public boolean isEmpty() {
		return size == 0;
	}

	// 移除特定索引的元素
	public void remove(int index){
		if(index<size){
			for(int i =index+1; i<size; i++){
				myArray[i-1]=myArray[i];
			}
		}
		size--;
	}
	
	//移除特定元素
	public void remove(T data){
		int index = 0;
		boolean match = false;
		for(int i =0; i<size; i++){
			if(myArray[i].equals(data)){
				index = i;
				match = true;
				break;
			}
		}
		
		if(!match){
			for(int i =index+1; i<size; i++){
				myArray[i-1]=myArray[i];
			}
		}
		size--;
	}
	
	//替换对应元素
	public void set(T data, int index){
		if(index<size){
			myArray[index] = data;
		}
	}
	
	//显示全部元素
	public void displayAll(){
		for(int i =0; i<size; i++){
			System.out.print(myArray[i]+" ");
		}
		System.out.print("\n");
	}
}

```

- MyArrayListTest 测试类

```
package arraylisttest;

public class MyArrayListTest {

	public static void main(String[] args) {
		MyArrayList<String> list = new MyArrayList<String>();
		
		list.add("111");
		list.add("222");
		list.add("333");
		list.displayAll();
		list.add("test", 1);
		list.displayAll();
		list.clear();
		System.out.println(list.isEmpty()); //true
		System.out.println("----------");
		
		list.add("aaa");
		list.add("bbb");
		list.add("aaa");
		list.add("ccc");
		list.add("eee");
		list.add("ddd");
		System.out.println(list.contains("ccc"));  //true
		System.out.println(list.contains("sss"));  //false
		System.out.println(list.get(2));  //aaa
		System.out.println(list.indexOf("aaa")); //0
		System.out.println(list.lastIndexOf("aaa"));  //2
		System.out.println(list.indexOf("ddd")); //5
		System.out.println(list.isEmpty());  //false
		System.out.println("----------");
		
		list.displayAll();
		list.remove(1);
		list.remove("ddd");
		list.displayAll();
		System.out.println("----------");
		
		list.set("bbb",1);
		list.displayAll();
		System.out.println("----------");
	}

}

```
