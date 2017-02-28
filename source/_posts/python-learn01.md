---
title: python学习笔记（一）
date: 2016-11-18 17:14:07
tags:
categories: read
---

### 数据类型

1. Number 整型(3.0以后不区分int和long)，浮点型
2. Bool 布尔类型 False True
3. String 字符串
4. 空值None,None与0不同
5. list列表   
   和其他语言中的组数一个概念，eg:L=[11,'ahui',['123','hehe']]
<!--more-->
6. tuple 元组   
和list不同的是tuple是不变的（是指tuple每个元素的指向是不变的），
定义时就赋值。元素访问和list一样。eg：t=(1,2,3)   
当tuple的某个元素是list时，list的元素是可变的，t=(1,[1,2],3)，t=()为空的tuple。只有一个元素时表示方法:t=(1,)多加逗号区别一般括号。
7. Dictionary 字典

### 序列

1. 内置六种序列    
字符串（不可变）、元组（不可变）、列表、Unicode字符串、   
2. 通用的属性   

    * 索引
    * 分片 eg:list[1:4]，加步长list[::4]  
    * 序列相加、序列相乘    
    * 获取长度，最大值最，小值

3. 字符串       

+ 长字符串   
用```'''字符串'''```来表示
+ 原始字符串
字符串前面加上r，如```r'test\n'```，r后面的字符串会原样输出。
