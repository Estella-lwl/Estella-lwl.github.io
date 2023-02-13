---
layout:
  - post
title: 使用InstanceType获取组件的类型
abbrlink: 26a2e475
author: polariis
top: true
cover: true
toc: true
mathjax: false
tags:
  - TypeScript
  - TypeScript在项目中使用&技巧
categories: TypeScript
date: 2023-01-07 14:22:23
img:
coverImg:
password:
summary:
---



## 1. InstanceType作用&使用

### **1.1 前言**：

- 使用<span style="color: #3850b8">`defineComponent`</span>的vue组件，导出的是一个<span style="color: #3850b8">对象</span>👇🏻（里面将对象传给了defineComponent方法，但它还会返回出来）

![image-20230212195244969](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230212195244969.png)

- - 👆🏻这个组件经defineComponent后导出的对象{}只是组件的描述（可以看成是个class类，但它里面是一个个的值，对比类型中的class），而其他组件引入后使用的是根据这个对象创建出的实例对象。

  - - 也就是说在其他组件引入后使用的是根据这个对象创建出的真正的**组件实例**，而不是直接使用这个组件导出的对象。

      ![image-20230212195455412](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230212195455412.png)



### 1.2 使用TS中的InstanceType获取组件的类型

- 所以需要找到使用组件类型的方法，也就引出了：<span style="color: #0091ff">**InstanceType**</span>。

- **InstanceType可以取到一个有构造函数的实例**，利用这一点<span style="color: #CC0000">**可以获取组件的类型**</span>。

- 先使用`typeof`拿到类型，再使用`InstanceType`拿到有构造函数的实例。这样就可以确认`demoRef`是`Demo`组件创建出来的。

- 在TS文档中：https://www.typescriptlang.org/docs/handbook/utility-types.html#instancetypetype

- - 文档中的例子👇🏻：（<span style="color: #E95200">**`C = InstanceType`**</span>，跟上张图中的意思一致）

![image-20230212195841335](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230212195841335.png)

- **示例**：

<span style="color: #0091ff">example2</span>. 使用typeof、InstanceType（TS提供的获取某个组件实例）：

- - 使用组件**对象的实例的类型**，而不是直接使用组件对象的类型：

```
// 引入子组件：
import LoginAccount from './login-account.vue'

// 拿到组件对象类型（LoginAccount是给自定义组件绑定的ref）：
const componentRef = ref<InstanceType<typeof LoginAccount>>();
```

<span style="color: #0091ff">example2</span>. 使用UI自带组件的类型：

```
import { ELForm } from 'element-plus';

// formRef是给el-form组件ref：
const formRef = ref<InstanceType<typeof ELForm>>();
```

