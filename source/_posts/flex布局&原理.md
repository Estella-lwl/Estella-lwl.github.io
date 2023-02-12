---
layout:
  - post
title: flex布局&原理
abbrlink: 14cadbc
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
categories: CSS
date: 2021-09-25 12:32:43
img:
coverImg:
password:
summary:
---



- <span style="color: #0091ff">**flex布局原理**</span>：

  - flex是flexible Box的缩写，意为：弹性布局。用来给盒子模型提供最大的灵活性，任何一个盒子模型都可以使用。
  - **当给父盒子设置flex布局后，子元素的float，clear，vertical-align属性将失效。**
  - 伸缩布局 = 弹性布局 = 伸缩盒布局 = 弹性盒布局 = flex布局。

-  子容器可以纵向排列也可以横向排列。

- 常见<span style="color: #0091ff">**父元素属性**</span>：

  - <span style="color: #0091ff">flex-direction</span>：设置<span style="color: #E95200">主轴的方向</span>。
    - 默认的主轴是x轴，row；那么y轴就是侧轴。
    - row：从左到右；
    - row-reverse：从右到左；
    - column：从上到下；
    - column-reverse：从下到上。

- - <span style="color: #0091ff">justify-content</span>：设置<span style="color: #E95200">主轴</span>上的<span style="color: #E95200">子元素的排列方式</span>。
    - flex-start：默认值：从头部开始。如果主轴是x轴，则从左往右。
    - flex-end：从尾部开始排列。
    - center：在主轴居中对齐（若主轴是x轴，则水平居中。）
    - space-around：平分剩余空间。
    - space-between：**先两边贴边，再平分剩余空间。**
  - <span style="color: #0091ff">flex-wrap</span>：设置<span style="color: #E95200">子元素是否换行</span>。<img src="https://s2.loli.net/2022/10/05/siH9OghT4WzdA38.png" alt="image.png" style="zoom: 80%;" />

- - align-content：设置侧轴上的子元素的排列方式（多行）。
  - align-items：设置侧轴上的子元素排列方式（单行）
  - flex-flow：复合属性，相当于同时设置了flex-direction和flex-wrap。

- 元素是跟着主轴来排列的。

- 可以把主轴设置为y轴，那么x轴就成了侧轴。
