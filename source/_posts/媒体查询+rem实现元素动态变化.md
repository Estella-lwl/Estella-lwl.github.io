---
layout:
  - post
title: 媒体查询+rem实现元素动态变化
abbrlink: 5e16b5cd
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
categories: CSS
date: 2021-09-25 21:09:55
img:
coverImg:
password:
summary:
---



## 1. rem

- em相对于父元素的字体大小来说的。
- rem相对于HTML元素字体大小来说的。1rem等于html元素上字体设置的大小。
- rem的优点就是可以通过修改HTML里的文字大小来改变页面中元素的大小 可以整体控制。



## 2. 媒体查询

- Media Query是C3的新语法。
- 使用@media查询，可以针对不同的媒体类型定义不同的样式。
- @media可以**针对不同的屏幕尺寸设置不同的样式。**
- 当重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。

### 语法规范：

```
@media mediatype and|not|only (media feature) {
    css code;
}
```

- mediatype：媒体类型
- 关键字：and   not   only
- media featrue：媒体特性，必须得有小括号包着。



### 1. mediatype查询类型：将不同的终端设备划分为不同的类型，称为媒体查询。

| 值     | 解释说明                         |
| ------ | -------------------------------- |
| all    | 用于所有设备                     |
| print  | 用于打印机和打印预览             |
| screen | 用于电脑屏幕，平板电脑，智能手机 |



### 2. 关键字将媒体类型或多个媒体特性连接到一起，作为媒体查询的条件。

- and：可以将多个媒体特性连一起，相当于“且”。用较多。
- not：排除某个媒体类型，相当于“非”，可以省略。
- only：指定某个特定的媒体类型，可以省略。



### 3. media feature媒体特性：每种媒体类型都有不同的特性，根据不同媒体类型的媒体特性设置不同的展示风格。

***注意**：他们要有小括号包着。

| 值        | 解释说明                             |
| --------- | ------------------------------------ |
| width     | 定义输出设备中页面可见区域的宽度     |
| min-width | 定义输出设备中页面最小可见区域的宽度 |
| max-width | 定义输出设备中页面最大可见区域的宽度 |

例：

```
// 这句话的意思就是：在屏幕上，且最大宽度是800px像素。
@media mediatype and (max-width: 800px) {
    body {
        background-color: pink;      /*当屏幕小于800px才会是粉色。*/
    }
}

@media mediatype and (max-width: 500px) {
    body {
        background-color: blue;      /*当屏幕小于500px才会是蓝色。*/
    }
}
```

在线效果演示： https://c.runoob.com/codedemo/5371/



## 3. 媒体查询+rem实现元素动态变化

- rem单位是跟着HTML的，有了rem页面元素可以设置不同大小尺寸。
- 媒体查询可以根据不同设备宽度来修改样式。
- 媒体查询+rem就可以实现不同设备宽度，实现页面元素大小的动态变化。

### 引入资源：

- 当样式比较多，可以针对不同的媒体引入使用不同的样式表。
- 原理：直接在link中判断设备尺寸，然后引入不同的CSS文件。
- 建议：媒体查询最好是从小到大的顺序。

例：当屏幕大于640px时，一行显示3个div；屏幕小于640px时，一行显示1个。

```
  <link rel="stylesheet" href="./css320" media="screen and (min-width: 320px)">
  <link rel="stylesheet" href="./css640" media="screen and (max-width: 640px)">
```

​        

## 4. rem的适配方案

### 4.1 rem适配的技术选用（市场主流）

#### 技术方案一：

- **less**
- **媒体查询**
- **rem**

**关于动态设置html标签的font-size大小**：

1. 假设设计稿是750px;
2. 把整个屏幕划分成15等份（也可以是10份20份，标准不一样）
3. 则每一份就作为html字体大小，这里就是50px；
4. 那么在320px设备时，字体大小：320/15=21.33px。
5. 这时会发现页面元素的大小除以不同的html字体大小的比例还是相同的。
6. 比如以750为标准的设计稿。一个100*100的元素在750屏幕下，就是100/50，转换为rem就是2rem，比例是一比一。
7.  320屏幕下，html字体大小为21.33，则2rem=42.66px，此时宽高都是42.66px，宽高依旧一比一。
8. 这时已经实现不同屏幕，元素等比例缩放。

**步骤总结**：

1. 选一套标准尺寸，750为准。
2. 屏幕宽度/划分的份数得到html font-size的大小。
3. 页面元素的rem值 = 页面元素值(px) / html font-size的大小。



#### 技术方案二（推荐）：

- **flexible.js**	（淘宝出的）
- **rem**

**总结**: 

1. 两种方案都存在；
2. 方案二更简单。

**PS**:

![image-20221005105215181](https://s2.loli.net/2022/10/05/l69pwZ1xkKJUXPf.png)

基本以750为准。



**代码示例**：

css：

```
 @media screen and (min-width: 320px) {
    html {
      font-size: 21.33px;
    }
  }

  @media screen and (min-width: 750px) {
    html {
      font-size: 50px;
    }
  }

  div {
    width: 2rem;
    height: 2rem;
    background-color: #fff;
  }
```

​         
