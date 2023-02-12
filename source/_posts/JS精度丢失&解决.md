---
layout:
  - post
title: JS精度丢失&解决
abbrlink: 7fcf66dd
author: polariis
top: true
cover: false
toc: false
mathjax: false
tags:
  - JavaScript
  - 更新中
categories: JavaScript
date: 2022-09-06 20:01:50
img:
coverImg:
password:
summary:
---



> 0.1 + 0.2 !== 0.3;

<span style="color: #0091ff">**精度丢失的原因**</span>：

- 计算机中所有的数据都是以<span style="color: #ff0001">二进制</span>存储的，所以在计算时计算机要把数据先转换成<span style="color: #ff0001">二进制</span>进行计算，然后在把计算结果转换成<span style="color: #ff0001">十进制</span>。
- <span style="color: #ff0001">二进制</span>计算发生了精度丢失，导致再转换成<span style="color: #ff0001">十进制</span>后和预计的结果不符。

<span style="color: #0091ff">**解决**</span>：

<span style="color: #0091ff">1.</span> 把小数放到位整数（乘倍数），再缩小回原来倍数（除倍数）。

0.1 + 0.2；改为 (0.1*10 + 0.2*10) / 10 == 0.3；  // true



<span style="color: #0091ff">2.</span> 

。。。
