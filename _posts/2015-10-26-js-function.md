---
layout : post
title : javascript 函数解析
category : web
tagline : "author: ChangSiyuan"
tags : [JavaScript]
---
{% include JB/setup %}

### 引言
- javascript中，函数是重要的机制，可以避免页面载入时执行该脚本，这些代码只能被事件激活，或者在函数被调用时才会执行；
- 本文总结了javascript函数的定义和调用方法，以供参考；

### javascript函数的定义
- 直接定义函数：

```
function test(){   
	alert("...");
}
```

- 通过函数字面量定义

```
var func = function(x){  
    alert(x);
}
```

- 通过构造函数定义：

```
var x = 1
var func2 = new Function(x,alert(x));  
```

