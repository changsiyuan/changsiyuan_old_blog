---
layout : post
title : Java命名规范
category : Java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- 编写java程序时，程序的易读性是评判程序好坏的很重要的指标，那么写java时，对象的命名要注意哪些方面呢？请看本节分解！
- 参考：[google java style](https://google-styleguide.googlecode.com/svn/trunk/javaguide.html#s5.2.5-non-constant-field-names)

### 命名注意事项
- Package names：全部小写，不要加特殊字符，比如`com.example.deepspace`，以下写法是错误的`com.example.deepSpace`，`com.example.deep_space`；
- Class names：写成UpperCamelCase，比如`HashIntegrationTest`，`Character `;
- Method names：写成lowerCamelCase，比如`sendMessage `，`testPop_emptyStack`；
- Constant names：写成全部大写形式，比如`CONSTANT_CASE`,`static final int NUMBER = 5;`,`static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");`,`static final Joiner COMMA_JOINER = Joiner.on(',');`,`static final SomeMutableType[] EMPTY_ARRAY = {};`；
