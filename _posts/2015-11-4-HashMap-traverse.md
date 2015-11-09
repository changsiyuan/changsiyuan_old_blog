---
layout : post
title : Java HashMap的遍历
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- HashMap是java中非常重要的数据结构；
- 本文提供了HashMap的若干种遍历方法，大体上分为两类：
  - 通过Map.Entry遍历；
  - 通过keyset遍历；

### HashMap遍历方法汇总

```
package test5;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;

public class MapTest {

	public static void main(String[] args) {
		Map<Integer, String> m = new HashMap<Integer, String>();
		m.put(1, "aaa");
		m.put(2, "bbb");
		m.put(3, "ccc");
		m.put(4, "ddd");
		m.put(5, "eee");

		// 遍历方法一
		for (Map.Entry<Integer, String> entry : m.entrySet()) {
			System.out.println("key:" + entry.getKey() + " value:"
					+ entry.getValue());
		}
		System.out.println("----------");

		// 遍历方法二
		Iterator<Entry<Integer, String>> it = m.entrySet().iterator();
		while (it.hasNext()) {
			Map.Entry<Integer, String> entry = it.next();
			System.out.println("key:" + entry.getKey() + " value:"
					+ entry.getValue());
		}
		System.out.println("----------");

		// 遍历方法三
		Set<Integer> s = m.keySet();
		Iterator<Integer> it1 = s.iterator();
		while (it1.hasNext()) {
			int i = it1.next();
			System.out.println("key:" + i + " value:"
					+ m.get(i));
		}
		System.out.println("----------");
		
		//遍历方法四
		for(int i : m.keySet()){
			System.out.println("key:"+i+" value:"+m.get(i));
		}
		System.out.println("----------");
		
		//遍历方法五  遍历所有的值  不能读取key
		for(String value:m.values()){
			System.out.println("value:"+value);
		}
	}
}

```
  
