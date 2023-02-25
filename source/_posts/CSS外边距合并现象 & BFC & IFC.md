---
layout:
  - post
title: CSS外边距合并现象 & BFC & IFC
abbrlink: b7cd257a
author: polariis
top: false
cover: true
toc: true
mathjax: false
tags:
  - CSS
  - BFC
categories: CSS
date: 2021-11-07 15:40:08
img:
coverImg:
password:
summary:
---



> **使用margin定义块元素的垂直外边距时，可能会出现外边距的合并**。



## 1. 外边距合并（塌陷）的两种情况

### 1.2 相邻块元素垂直外边距的合并：

- 当上下两个相邻的两个块元素（兄弟元素）相遇时，如果上面的元素有下边距 `margin-bottom`，下面的元素有上边距 `margin-top`。则它们之间的距离不是两者之和，而是其一的最大值。
- **解决方案**：避免这种写法。



### 1.2 嵌套块元素垂直外边距的合并：

- 对于两个嵌套关系（父子）的块元素，父元素有上边距，同时子元素也有上边距，此时父元素会塌陷较大的外边距值。

- **解决方案**：

- - 给父元素定义上边框；
  - 给父元素定义上内边距；	(和上一种一样，都是与子元素分开)
  - 给父元素添加：`overflow: hidden;`  使其成为 **BFC**（比较常用）
  - 还有其他方法，比如浮动、固定、绝对定位的盒子不会有塌陷问题。

- 

## 2. BFC

- **定义**：BFC是块级格式化上下文，它有独立的渲染区域，不会影响外部元素。

- **触发条件**：  （满足任意一条就会触发）

- - HTML根元素
  - 浮动元素
  - 绝对定位元素
  - overflow 除了 visible 以外的值 (hidden、auto、scroll)。
  - display 为 inline-block、table-cells、flex。

- **BFC 作用 （使用场景）**：

- 1. **阻止外边距塌陷**：比如设置 overflow：hidden  。（👆🏻第一小节中的情况）

  2.  **包含浮动元素** 

  3. - **场景**：当子元素浮动，撑不起父元素高度发生了塌陷，就可以对父元素使用 `overflow：hidden`，使父元素触发 BFC。

  4.  **避免元素被浮动元素覆盖** 

  5. - **场景**：左侧元素浮动，右侧不浮动，左侧被遮盖， 对右侧元素添加 `overflow: hidden` 。

  6. 自适应两栏 / 三栏布局 。