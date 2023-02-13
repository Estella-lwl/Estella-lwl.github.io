---
layout:
  - post
title: JavaScript中的操作符
abbrlink: 122feaea
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - JavaScript
categories: JavaScript
date: 2022-11-06 19:36:00
img:
coverImg:
password:
summary:
---



1. ## !! 和 ??

### 1.1  !!的含义：

- 在JS中，当想把一个数据**转为布尔类型**时：

- - 可以直接**取反**，这样返回的就是布尔类型；但这样值也就反了，所以**再取反**得到正确的值。

- 类似于 Boolean(变量) 的方式。



### 1.2  ??  空值合并操作符：

- ES11的新特性；

- 空值合并操作符是一个逻辑运算符。当左侧为null / undefined时返回右侧值；否则返回左侧值。

- - <span style="background-color: #f2f4f5;">相当于在null时返回一个默认值</span>，而不是直接返回null。

- 看以下**对比**：

  ```
  // 当这里赋值为null时：
  let message: string | null = null;
  // 这里会返回该值：
  //let message: string | null ac '有值，返回吧';  
  
  const content = message ?? '左侧为null所以返回我';
  ```

  
