---
title: CSS定位机制
abbrlink: 5d7648ee
author: polariis
top: true
cover: true
toc: true
mathjax: false
categories: CSS
tags:
  - CSS
  - 基础回顾
date: 2020-04-05 00:02:56
img:
coverImg:
password:
summary:
---

## 一.文档流flow

- 从上到下，从左到右。

- ### 1.block元素：

  - 元素独占一行；

  - 元素的height、width、margin、padding都可以设置。

  - 常见的block元素：div、p、h1~h6、ol、ul、table、form

  - 将元素显示为block元素：**display:block;**

    例：

  ```
  a{
  	display:block;
  }
  ```

  inline元素a转为block，从而使a元素具有块状元素的特点。

- ### 2.inline元素：

  - 不用单独占用一行；
  - height、width不可设置；
  - width就是它已包含的文字或图片的宽度，不可改变。
  - 常见inline元素：span、a
  - 将元素显示为inline元素：**display:inline;**
  - inline元素之间有个间距的问题，解决方法：转换为block。

- ### 3.inline-block元素：

  - 同时具备inline和block的特点：可平行的block；
  - 不单独占用一行；
  - 元素的height、width、margin、padding都可以设置；
  - 常见的inline-block元素：img
  - 显示为inline-block元素：**display:inline-block；**

- ### 4.相互转换：

  - display:none;			     元素不会被显示（**不占原位置**）
  - display:block		          显示为block元素
  - display:inline	              显示为inline元素
  - display:inline-block		显示为inline-block元素

## 二.浮动定位float

- float属性：left、right、none（不浮动）；

- clear属性（清除）：left、right、both。

  </br>

**当有3列都要水平时，将3列都设为左浮动即可。（宽度够用时）*

### float属性的特点：

- 当其中一个盒子移动时，**其原有位置丢失，并被其他元素占用位置。**

  </br>

**3个盒子并行，当高度不够时：*

- 第一种情况：box3下降
- 第二种情况：box1较高，box3向左下降时被卡住。

### clear属性：

- 单方向清除浮动的发法：比如将一行的box1和box2变为上下的列关系

  - 使box1位置不变，将box2设为：

  - ```
    clear:right; 
    ```

      意思是清除右侧浮动，使其占据右侧。
    
    </br>

- 双侧清除：

  ```
  clear:right;
  ```

  意思是清楚两侧的浮动，使设置的元素单独占一行。

## 三.层定位layer

### position属性：（相对于谁定位）

- static：      默认值（没有定位，相当于不是层定位，定位的属性失效）

- fixed：      固定定位（相对于浏览器窗口定位）

- relative： 相对定位（相对于原来自己的位置定位），元素会脱离文档流，但在文档流中的**位置还在，不会被占据。**

- absolute：绝对定位（相对于static以外的第一个父元素定位），元素脱离文档流，在文档流中的**原位置不再存在，会被占据**。

  </br>

概述：像图像软件中的图层一样可以对每个layer进行精准定位操作。

</br>

*当子元素相对于父元素定位，且父元素也相对于其父元素定位。可将最外层用相对定位，**里面两层用绝对定位。***

</br>

**特殊情况;：*

*对于absolute定位的层，若其父层都未定义absolute或relative，则将会**相对body进行定位**。*

</br>

**relative+absolute：**（父相子绝）

- 父元素box1：position: relative;
- 子元素box2：position: absolute;
- 子元素box2：left、right、top、bottom相对于父元素进行偏移定位。

**好处**：当父元素box1移动时，子元素会相对于父元素的位置进行定位，可以一起移动。

### 定位的属性：

- left属性
- right属性
- top属性
- bottom属性
- z-index属性  （值大在上面）

**当想把图片设为背景，可将z-index设为负数，以留在最底层。*

</br>

</br>

