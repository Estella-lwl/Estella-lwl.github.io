---
title: CSS添加方法&优先级&CSS选择器
abbrlink: 6a1f6cdf
author: polariis
top: true
cover: false #是否放入首页轮播
toc: true
mathjax: false
categories: CSS
tags:
  - CSS
  - 基础回顾
date: 2020-03-31 00:02:56
img:
coverImg:
password:
summary:
---

## 一、CSS添加方法：

### 1.行内样式：

表示当前样式作为CSS样式被添加到标签上（只对这个标签起作用）。

### 2.内嵌样式：

将CSS代码内嵌到当前页面的head标签部分（只对当前页面起作用）。

首先body部分不再包含样式，将样式放在style标签中表示，和head标签为嵌套关系。

### 3.链接样式（单独文件）：

```
<link rel="stylesheet" href="css/style.css">
```

stylesheet为样式表文件；

href后面的是相对路径。

## 二、样式优先级：

- [ ] 多重样式可重叠，可覆盖。
- [ ] 样式的优先级按”就近原则“。
- [ ] 行内样式>内嵌样式>链接样式>浏览器默认样式

## 三、CSS选择器：

### 1.标签选择器：

与标签同名，紧跟着一对花括号，例如：

```
body{
	border-color: #aaaaff;
}
```



### 2.类选择器：

以.开头，例:

```
.one{
	font-size:18px;
}
```



### 3.ID选择器：

以#开头，例:

```
#two{
	font-size:18px;
}
```

2和3的区别：

- [ ] ID是唯一的，只能在HTML被引用一次。
- [ ] 类选择器可以被多次引用，应用中多个页面元素上。
- [ ] *因此：ID选择器用来布局，class选择器用在样式。

### 4.后代选择器：

```
p span{
	clor:red;			//作用于p标签内部的span标签
}
```

两个标签之间使用空格空开。

### 5.并集选择器：

```
p,span{
	clor:red;			//同时作用于两个标签
}
```

连接符为,会使两者都变为红色。

### 6.伪类选择器(Pseudo-classes)：

语法：

```
selector:pseudo-class {property:value;}
```

例如：

```
a:link    		//普通的，未被访问的链接
a:visited 		//用户已经访问过的链接
a:hover 		//鼠标悬停于链接上的样式
a:active 		//链接被点击时的样式
```

​	注意：必须严格遵守以下顺序。

a:hover			**必须在a:link和a:visited之后**

a:active 			**必须在a:hover之后**

**口诀：**

**l**o**v**e&**ha**te

### 7.混合：

- 多个class选择器混用，用空格分开。

```
<div class="one yellow left">
</div>
```



- id和class混用：

```
<div id="my" class="one yellow left">
</div>
```

- id选择器不可以多个同时使用。

</br>

</br>

</br>