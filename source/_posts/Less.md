---
layout:
  - post
title: Less
abbrlink: 73843be1
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
  - Less
categories: CSS
date: 2021-11-25 22:31:52
img:
coverImg:
password:
summary:
---



CSS的弊端：

<img src="https://s2.loli.net/2022/10/05/aV2fh4OoXpzNFKr.png" alt="image.png" style="zoom:80%;" />

------

# Less

## 1. Less介绍

- Leaner Style Sheets，是一门CSS扩展语言，也称为<span style="color: #FFB700">CSS预处理器</span>。
- 作为CSS的一种形式的扩展，在现有的CSS语法上为CSS加入程序式的特性。
- 它在CSS的语法上，引入了<u>变量，Mixin（混入），运算以及函数</u>等功能，简化了CSS的编写，降低了维护成本。
- 既可以在客户端运行，也可以在服务器端运行（Node、Rhino平台）。
- Less中文网址：http://lesscss.cn/
- 中文文档：https://less.bootcss.com/
- 常见的CSS预处理器：<span style="background-color: #f2f4f5;">Sass、Less、Stylus</span>。



## 2. Less使用

### 2.1 Less编译为CSS

**方式一**：less文件需要编译，在**官网下载less.js文件**，引入该文件。

需要注意：

- 引入的less文件要放在js编译文件之上，否则没效果。
- <span style="background-color: #f2f4f5;">rel="stylesheet/less" </span>，在原先写法上加 <span style="background-color: #f2f4f5;">/less</span>。

```
<link rel="stylesheet/less" href="">
<script src="/less.js"> </script>
```

**方式二   Vscode less 插件：**

- <span style="color: #ff0001">**Easy LESS**</span>**插件用来把less文件编译为css文件。**
- **安装完插件后，重新加载下Vscode。只要保存一下less文件，会自动生成css文件。**
- 在写样式时只需关注less文件，对应的css文件会自动更新。

### 2.2 Less的注释

- <span style="color: #ff0001;background-color: #f2f4f5;">// </span>：这种注释在编译后不会出现在css文件中。
- <span style="color: #ff0001;background-color: #f2f4f5;">/* */</span>：这种编译后会出现在css文件中。

------



## 3. Less变量

### 3.1 几种变量的使用

#### 1. 选择器变量

将选择器名称写为变量。

举例：

```
@boxSelector: #wrap;
@wrap: wrap;

使用：
@{boxSelector} {
    color: #bbb;
    width: 200px;
}
.@{wrap} {
    color: #bbb;
    width: 200px;
}

最后编译的css为：
#boxSelector {
    color: #bbb;
    width: 200px;
}
.wrap {
    color: #bbb;
    width: 200px;
}
```



#### 2. 属性变量

- <span style="color: #FFB700">属性名</span>和<span style="color: #FFB700">属性值</span>都可以是变量。
  - **语法**： <span style="background-color: #f2f4f5;">**@{ xxx }: @yyy;**</span>
  - **注意**：<span style="color: #0091ff">属性名</span>要被一对<span style="color: #0091ff">花括号</span>包起来，而<span style="color: #0091ff">值不用</span>。
- 举例：

```
定义变量：
@w: width;
@w-value: 100px;

使用：
 .box{
     @{w}: @w-value;
 } 
```



#### 3. URL变量

方便拼接，后续路径更改只需改一次变量中的值。

举例：

```
@images: "../img/";

.box {
    background: url('@images/除变量以外需要拼接的路径.png');
}
```



#### 4. 声明变量

相当于<span style="color: #ff0001">属性名和值一起</span>。

- 使用变量的语法：<span style="color: #E95200;background-color: #f2f4f5;">@bg();</span>；
- 注意后面需要加上 <span style="color: #E95200;background-color: #f2f4f5;"> () </span> 。

举例：

```
@bg: { background: #fff; };

.box {
    @bg();
}
```



#### 5. 变量的运算

**1. 用法示例**：

```
@w = 100px;
div {
    width: @w - 50;
}
```

**2.** <span style="color: #CC0000">**注意点：**</span>

- 运算符<span style="color: #FFB700">左右</span>必须有个<span style="color: #ff0001">**空格**</span>；
- 变量的<span style="color: #ff0001">**单位**</span>问题：
  - 加减法：<span style="color: #E95200">**取第一个**</span>为准。
  - 乘除法：<span style="color: #E95200">**单位一定要统一**</span>。



#### 6. 变量的作用域 & 用变量定义变量

**作用域：就近原则**，和css的处理方式是一样的。

两种结合起来举例：

```
@width: @a;
@a: 100%;

.div {
    width: @width;
    @a: 9%
}

以上被解析为：
width: 9%;
个人理解：不仅是变量的就近原则，还有下面样式覆盖上面的因素在。
```



#### 7. 混合

- 可以带参数。。。。(此处待记......)
- 可以有多个参数。

![image-20221005113444528](https://s2.loli.net/2022/10/05/kcuTQpieW9aqBwC.png)

### 3.2 **变量命名规范**

- **必须含有**<span style="color: #ff0001">**@为前缀**</span>；
- **不能包含**<span style="color: #ff0001">**特殊字符**</span>；
- **不能以数字开头**；
- **大小写敏感**。（@color和@<span style="color: #ff0001">C</span>olor是两个变量）

例：

```
// 定义一个蓝色的变量：
@color: pink;
body {
    background: @color;
}
```



## 4. Less嵌套

### 4.1 &符的使用

- 当结构有**嵌套**时，**使用** **<span style="color: #FFB700">&</span> 与不使用的区别**：
  - 使用 &符会不发生嵌套，而是直接替换了。
- 有**伪类/伪元素**时需要用<span style="color: #FFB700">**&**</span>。

**例1：**

```
#header {
    &:after {
        content: 'less is more';    
    }
    .title {
        font-weight: bold;    
    }
    &_content {
        margin: 20px;    
    }
}


// 生成的css： 下面注意区分
#header::after {
    content: 'less is more';
}
#header .title {   // 嵌套了
    font-weight: bold;
}
#header_content {   // 没嵌套！！！替换了
    margin: 20px;
}

```

**例2**：

```
<div class="logo">
  <a href="">这是一个网站名称</a>
</div>
```

less的写法：

```
.logo {
  width: 100px;
  a {
    color: blue;
  }
}
```

**例3：**当有**伪类/伪元素**时，用**&**。

例：

```
a {
    color: blue;
}

a:hover {
    color: green;
}
```

**要写成：**

```
a {
    color: blue;
    // 要变成这样
    &:hover {
        color: green;
    }
}
```



### 4.2 媒体查询

在css中，不同元素样式要在每个尺寸下都写，Less可以在写当前元素样式时顺带写媒体查询（如下）。

**缺点**：每个元素汇编出自己的 @media 声明，并**不会合并**。

```
#wrap {
    width: 500px;
}
@media screen and (max-width: 768px) {
    #wrap {
        width: 100px;    
    }
}

// 使用Less：
#main {
    @media screen {
        @media (max-width: 768px) {
            width: 100px;        
        }    
    }
    @media tv {
        width: 100px;      
    }
}
```



### 使用技巧——定义私有样式  （待续...）

------





## 5. 匹配模式——条件判断

条件符合时使用该样式渲染页面。

https://www.bilibili.com/video/BV13F411Y7ro?p=7&vd_source=d839eda9a2b875da47ce4ee8814c818f&t=427.5

举例：

  <img src="https://s2.loli.net/2022/10/05/rwnDo5TUtB6O29u.png" alt="image-20221005113936555" style="zoom:80%;" />

如何使用上面的判断：同样在括号中放入参数，进入匹配模式。

  ![image.png](https://s2.loli.net/2022/10/05/1DQS9yq2u5Nvcn8.png)



## 6. 导入

https://www.bilibili.com/video/BV1fS4y127nt?p=6&vd_source=d839eda9a2b875da47ce4ee8814c818f&t=11.0

**1.** <span style="color: #0091ff">**文件导入**</span>：可以不加文件后缀名。

```
// 在index.less文件中导入nav.less：
@import "nav";
```

**2.** <span style="color: #0091ff">**reference**</span>：引入Less文件，而不编译它。

```
@import (reference) "nav";
```

**3.** <span style="color: #0091ff">**once**</span>：@import语句的**<u>默认行为</u>**。表明相同的文件只会被导入一次，随后导入的文件的重复代码不会再解析。

```
@import (once) "nav";
```

**4.** <span style="color: #0091ff">**multiple**</span>：<u>允许导入**多个同名文件**</u>。

```
@import (multiple) "nav";
```

