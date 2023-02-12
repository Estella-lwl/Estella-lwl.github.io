---
layout:
  - post
title: CSS样式的继承
abbrlink: dbc5d360
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
categories: CSS
date: 2021-11-07 16:03:12
img:
coverImg:
password:
summary:
---



## 1.行高的继承

```
font:12px/24px 'Microsoft YaHei';              
```

指的是行高24px。

```
font:12px/1.5 'Microsoft YaHei';
```

- 指的是让子元素继承父元素1.5，而且是子元素字体大小*1，5。
- 行高可以有或没有单位。
- 例：

<img src="https://s2.loli.net/2022/10/05/kgiL2KZDFUzonf4.png" alt="image.png" style="zoom: 50%;" />

## 2. 权重的注意点

### 2.1 选择器权重：

  <img src="https://s2.loli.net/2022/10/05/2VbphskzyWPR8an.png" alt="image.png" style="zoom: 67%;" />

### 2.2 注意：继承的权重是0！！！！！！

- 所以当父元素设置了属性，其子元素也设置并且属性值冲突时的时候，子元素并不会继承，而是按照自己给的值显示。因为就算父元素权重再大，继承的权重也是0。
- 再比如最近写Vplus官网碰到的，给body设置颜色，但a便签依旧没变，这是因为继承过来为0，同时在浏览器中，a是有自己的默认样式的，所以自然影响不到。

### 2.3 权重会叠加，但不会出现进位。



