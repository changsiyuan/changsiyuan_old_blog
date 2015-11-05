---
layout : post
title : javascript字符串操作
category : web
tagline : "author: ChangSiyuan"
tags : [JavaScript]
---
{% include JB/setup %}

### 引言
- 字符串操作是js中常见的操作；
- 本文总结了js中常见的字符串操作；

### 常见字符串操作

```
var str1 = "aaa";
var str2 = "bbb";
var str3 = "ccc";
var str4 = "aaabbbccc";
var str5="Hello world!"

//字符串长度
document.write(str1.length +"<br>")

//大小写转换
document.write(str5.toLowerCase() +"<br>")  //hello world!
document.write(str5.toUpperCase() +"<br>")  //HELLO WORLD!

//连接两个字符串
document.write(str1.concat(str2) +"<br>")  //aaabbb

//返回字符串中一个子串第一处出现的索引。如果没有匹配项，返回 -1 
document.write(str4.indexOf("b") +"<br>")  // 3
document.write(str4.indexOf("c") +"<br>")  // 6
document.write(str4.indexOf("ab") +"<br>")  // 2

//返回字符串中一个子串最后一处出现的索引，如果没有匹配项，返回 -1
document.write(str4.lastIndexOf("b") +"<br>")  // 5

//返回指定位置的字符
document.write(str4.charAt(2) +"<br>")  // a
document.write(str4.charAt(7) +"<br>")  // c

//使用 match() 来检索一个字符串
document.write(str5.match("world") + "<br />")  // world
document.write(str5.match("World") + "<br />")  // null
document.write(str5.match("worlld") + "<br />")  // null
document.write(str5.match("world!") + "<br />")  // world!

//使用 match() 来检索一个正则表达式的匹配
document.write(str4.match(/a*b/) + "<br />")  // aaab

//返回字符串的一个子串,传入参数是起始位置和结束位置
document.write(str4.substring(0,5) + "<br />")  //aaabb

//slice() 方法可提取字符串的某个部分，并以新的字符串返回被提取的部分
//stringObject.slice(start,end)
document.write(str4.slice(1,4) + "<br />")  // aab

//替换部分字符串，stringObject.replace(regexp/substr,replacement)
document.write(str4.replace("aaab","ccc") + "<br />") //cccbbccc
document.write(str4.replace(/a*b/,"ccc") + "<br />")  //cccbbccc

//search() 方法用于检索字符串中指定的子字符串,或检索与正则表达式相匹配的子字符串
//stringObject.search(regexp)
document.write(str4.search(/a*b/) + "<br />")  // 0
document.write(str4.search(/b*c/) + "<br />") // 3
document.write(str4.search(/.*d/) + "<br />") // -1(无匹配)

//通过将字符串划分成子串，将一个字符串做成一个字符串数组
document.write(str5.split(" ")[0] + "<br />")  //Hello
document.write(str5.split(" ")[1] + "<br />")  //world!
```
