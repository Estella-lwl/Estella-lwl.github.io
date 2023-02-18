---
layout:
  - post
title: 关于TS中描述Object类型的两种方式（类型签名和Record）
abbrlink: 61dca9cf
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - TypeScript
  - 更新中
categories: TypeScript
date: 2022-12-10 16:18:02
img:
coverImg:
password:
summary:
---

**前言** ：由于 Object 的范围很大，对象中属性可能是任意类型，而这时区分不出一个 Object 具体是什么类型。

所以一般不在 TS 中直接使用 Object 类型，而是以下两种方式描述对象：

1. **class / constructor**；
2. **type**或**interface**，推荐用这种，更灵活。

## 方式一：类型签名

举例 1：

```
type Obj = {
    [K: string]: number  //代表Obj中有一个字符串类型属性（具体属性名可以随便取只要是字符串就可），其属性值是数字。
}
```

- - 使用上有个共识，如果是泛型使用大写 K；若非泛型，是用小写 k。

上例基础上举例 2：

```
type Obj = {
    [K: string]: number
}

const a: Obj = {
    name: 1
    123： 6  //虽然使用type规定了属性名为string，而这里写了数字，但会编译通过。
}
```

- - 例子中属性名的命名为数字编译会通过，是因为在 JS 层面：所有属性名最终被都是字符串形式。

## ![image-20230211093401215](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230211093401215.png)

由例 2 引申的例 3：JS 中**一个矛盾的情况**。 （👈🏻 看下面的总结，number 类型也可以）

```
type Obj = {
    [K: number]: number   //注意看这里，给属性名规定为number类型。
}

const a: Obj = {
    name: 1   //这里无法编译通过，因为JS中所有key都是字符串（string或symbol）。
    123： 6   //奇怪的在这行，既然key只能字符串但这里number居然编译通过了！！！！！
}
```

- - **PS**：**当 symbol 作为 key**，必须**用中括号包**一下。
  - 这篇文章中提到了这个情况：[深入理解 TS——签名索引](https://jkchao.github.io/typescript-book-chinese/typings/indexSignatures.html#typescript-索引签名)

-

综上，**总结**：

- 对象中 key 的类型可以不是 string 吗？

- - 还可以是 symbol 或 number。

## 方式二：Record

其余待整理
