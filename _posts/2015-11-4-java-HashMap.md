---
layout : post
title : Java HashMap的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- Java中，HashMap是重要的数据结构；
- HashMap用散列表实现，具体来说：
  - HashMap底层维护一个数组充当哈希表；
  - 当向HashMap中put一对键值时，它会根据key的hashCode值计算出一个位置（计算方法如下图），该位置就是此对象准备往数组中存放的位置，然后将key、value、next等值存放在Bucket中，作为Map.Entry；
  - 如果有哈希值的冲突，即两组数据要放在数组的同一个位置中，则用链表实现；
  - 当从HashMap中get一个键值对时，HashMap会使用键对象的hashcode找到bucket位置，然后获取值对象；
  - 如果数组的同一个位置对应多个键值对，将会遍历LinkedList，并调用keys.equals()方法去找到LinkedList中正确的节点，最终找到要找的值对象；
- 本文实现了一个简单的HashMap；

### HashMap具体实现
- Entry类，定义节点

```
package MyHashMap;

import java.util.Objects;

public class Entry<K, V> {
	K k;
	V v;
	Entry<K, V> nextEntry = null;

	// 构造器
	public Entry(K k, V v) {
		this.k = k;
		this.v = v;
		this.nextEntry = null;
	}

	public void setNextEntry(Entry<K, V> e) {
		this.nextEntry = e;
	}

	public Entry<K, V> getNextEntry() {
		return this.nextEntry;
	}

	public int hashCode() {
		return Objects.hashCode(k) ^ Objects.hashCode(v);
	}

	public void display() {
		System.out.println("key:" + k + " value:" + v);
	}
}

```

### MyHashMap类，实现HashMap大部分方法

```
package MyHashMap;

public class MyHashMap<K, V> {
	public static final int DEFAULT_INITIAL_CAPACITY = 16;

	protected Entry<K, V>[] entry = new Entry[DEFAULT_INITIAL_CAPACITY];
	protected int size = 0; // 记录hashmap大小
	protected int bucketSize = 0;

	// 清空hashmap
	public void clear() {
		entry = new Entry[DEFAULT_INITIAL_CAPACITY];
		size = 0;
	}

	// 检测数组大小
	public void testSize() {
		int length = entry.length;
		if (bucketSize >= length / 2) {
			Entry<K, V>[] newEntry = new Entry[length * 2];
			System.arraycopy(entry, 0, newEntry, 0, length);
		}
	}

	// 计算元素在数组中的位置
	public static int indexFor(int h, int size) {
		return h % (size - 1);
	}

	// 是否含有特定key
	public boolean containsKey(K k) {
		int index = indexFor(k.hashCode(), entry.length);
		if(entry[index]!=null){
			if(entry[index].k.equals(k)){
				return true;
			}else{
				Entry<K,V> e = entry[index].nextEntry;
				while(e!=null){
					if(e.k.equals(k)){
						return true;
					}
				}
			}
		}
		return false;
	}

	// 是否包含特定value值
	public boolean containsValue(V v) {
		for (int i = 0; i < entry.length; i++) {
			if (entry[i] != null) {
				if (entry[i].v.equals(v)) {
					return true;
				} else {
					if (entry[i].nextEntry != null) {
						Entry<K, V> e = entry[i];
						while (e != null) {
							if (e.v.equals(v)) {
								return true;
							}
							e = e.nextEntry;
						}
					} else {
						continue;
					}
				}
			}
		}
		return false;
	}

	// 获取特定key的value值
	public V get(K k) {
		int index = indexFor(k.hashCode(), entry.length);
		if(entry[index]!=null){
			if(entry[index].k.equals(k)){
				return entry[index].v;
			}else{
				Entry<K,V> e = entry[index].nextEntry;
				while(e!=null){
					if(e.k.equals(k)){
						return e.v;
					}
				}
			}
		}
		return null;
	}

	// 插入数据
	public void put(K k, V v) {
		testSize();
		int index = indexFor(k.hashCode(), entry.length); // 计算这个元素在数组中的位置

		Entry<K, V> e = new Entry<K, V>(k, v);
		if (entry[index] == null) {
			entry[index] = e;
			bucketSize++;
		} else {
			Entry<K, V> ee = entry[index];
			ee.setNextEntry(e);
		}
		size++;
	}

	// 删除数据
	public void remove(K k) {
		int index = indexFor(k.hashCode(), entry.length);
		if(entry[index]!=null){
			Entry<K,V> e = entry[index];
			if (e.nextEntry == null) {
				if (e.k.equals(k)) {
					entry[index] = null;
					bucketSize--;
					size--;
				}
			} else {
				if (e.k.equals(k)) {
					entry[index] = e.nextEntry;
					size--;
				} else {
					Entry<K, V> previous = e;
					Entry<K, V> current = e.nextEntry;
					while (current != null) {
						if (current.k.equals(k)) {
							previous.nextEntry = current.nextEntry;
							size--;
							break;
						}
						previous = current;
						current = current.getNextEntry();
					}
				}
			}
		}
	}

	// 判断是否为空
	public boolean isEmpty() {
		return size == 0;
	}

	// 获取长度
	public int size() {
		return size;
	}

	// 遍历输出
	public void diaplayAll() {
		for (int i = 0; i < entry.length; i++) {
			if (entry[i] != null) {
				if (entry[i].nextEntry != null) {
					Entry<K, V> e = entry[i];
					while (e != null) {
						e.display();
						e = e.nextEntry;
					}
				} else {
					entry[i].display();
					continue;
				}
			}
		}
	}
}

```

### TestMyHashMap测试类

```
package MyHashMap;

public class TestMyHashMap {

	public static void main(String[] args) {
		MyHashMap<String, String> mhm = new MyHashMap<String, String>();
		mhm.put("1", "111");
		mhm.put("2", "222");
		System.out.println(mhm.size());  //2
		mhm.clear();
		System.out.println(mhm.isEmpty());  //true
		System.out.println("-------------");
		
		mhm.put("1", "aaa");
		mhm.put("6", "fff");
		mhm.put("2", "bbb");
		mhm.put("5", "eee");
		mhm.put("4", "ddd");
		mhm.put("3", "ccc");
		System.out.println(mhm.size());  //6
		mhm.remove("2");
		System.out.println(mhm.size());  //5
		System.out.println(mhm.containsKey("5"));  //true
		System.out.println(mhm.containsKey("2"));  //false
		System.out.println(mhm.containsKey("3"));  //true
		System.out.println(mhm.containsValue("ccc"));  //true
		System.out.println(mhm.containsValue("ggg"));  //false
		System.out.println(mhm.get("6"));  //fff 
		System.out.println(mhm.get("5"));  //eee
		System.out.println(mhm.get("2"));  //null
		System.out.println(mhm.isEmpty());  //false
		System.out.println("-------------");
		
		mhm.diaplayAll();
	}

}

```

