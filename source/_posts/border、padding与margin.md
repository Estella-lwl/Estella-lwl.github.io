---
layout:
  - post
title: border、padding与margin
abbrlink: 553bfec9
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
categories: CSS
date: 2021-11-10 11:33:53
img:
coverImg:
password:
summary:
---



**场景**：鼠标悬停出现内边框，位置不是写死的，所以导致li上下动。

**解决**：添加padding。且padding值 = -border里的数字。

```
border: #00FFFF 5px inset;
margin: -5px;
```

**PS**：如果是外边框，就添加margin。

```
border: #00FFFF 5px inset;
padding: -5px;
```

（一开始我想反了，觉得内边框加padding。因为觉得内边框在里面，内容撑大了盒子所以要padding为负来解决。）

但刚好相反。

- 另外，关于**border颜色不一。**

原先：

```
border: #00FFFF 5px inset; 
```

改成solid：

```
border: #00FFFF 5px solid;
```

​            

