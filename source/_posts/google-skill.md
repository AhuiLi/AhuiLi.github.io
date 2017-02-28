---
title: google搜索技巧与常用命令
date: 2015-12-12 22:18:05
categories: php
tags: [google,搜索,google搜索技巧]
---

## 基本搜索

- 准确搜索（关键字加引号"" 例如："云思 科技"）
- 逻辑操作（+,-）  
    > +逻辑与，明文可以不写，空格区分。  
    > -逻辑非，表示不含有（云思科技 -郑州）
- 同义字搜索（~）
- 星号（通配符）
- 《》书名号（百度特有，两层含义，1. 其内容不会被拆分 2. 搜索的内容含有书名号）
- 显示指定年份的搜索结果（云思 2015..2016）
<!--more-->
-------------------------------------------

## 搜索命令

- filetype:(只搜索指定类型的文档)
    > pdf
    > jpg
    > doc
    > ...
- intitle:(只显示标题中包含指定关键字的搜索结果)
- site:cloudxink.com (不包含http://,该域名下的所有页面)
- inurl:(云思科技)
- allinurl:(与inurl区别是返回包含所有关键字的url)
- allintitle:(同上)
- intext

## 搜索小技巧

- define:(词的定义)
- 数学计算(直接输入运算公式)
- 单位换算(51厘米=?米)
- 翻译:(词的英文翻译)

## 快捷键

- ctrl+F(可以搜索当前搜索结果页面某个关键词)
- ctrl+L(选中地址栏)
- ctrl+tab(切换标签页,mac有区别)
- do a barrel roll(玩，百度搜索旋转，黑洞....)

## 链接

- [谷歌高级搜索](https://support.google.com/websearch/answer/2466433)
- https://support.google.com/websearch/answer/35890?hl=zh-Hans
- [百度高级搜索](https://www.baidu.com/gaoji/advanced.html)

--------------------------------------------

## 建议

1. 先明确自己需要搜索什么
2. 把想搜索的内容进行关键字拆分，命令组合。尽量避免一段话或者直接询问的方式。
3. 针对比较专业的搜索内容，尽量积累一些网站。比方说找开源代码去github，找论文去google学术，中国知网等。

## 题外扩展

- sublime安装MarkDown Editing插件支持MarkDown语法
- MarkdownPreview插件生成网页html

``` php
public function getname(){
    $name = 1;
}

```
