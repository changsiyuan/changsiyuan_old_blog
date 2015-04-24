---
layout : post
title : MapReduce过程详解
category : Hadoop
tagline : "author: ChangSiyuan"
tags : [Hadoop]
---
{% include JB/setup %}

### 引言
- mapreduce的过程看似简单，其实，要保证数据处理过程中的高效率、高容错性，mapreduce中有很多精巧的机制值得我们学习和参考。本文主要介绍mapreduce的过程以及其中涉及到的重要的思想。
