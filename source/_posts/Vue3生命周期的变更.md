---
title: Vue3生命周期的变更
abbrlink: 2606f5b3
author: polariis
top: true
cover: false
toc: true
mathjax: false
categories: Vue3
tags:
  - Vue3
typora-root-url: imgs
date: 2022-08-07 09:07:21
img:
coverImg:
password:
summary:
---

##

- 销毁的概念改为**卸载**（unmounted），更名：

- - beforeDestroy 改名为 beforeUnmounted。
  - destroyed 改名为 unmounted。

- vue2 中的两次判断改为一次。

​ ![image-20220810030613459](C:\Users\失频本人\Desktop\blog修改历史\blog\source_posts\image-20220810030613459.png)

- Vue3 提供的**Composition API**形式对比：

- - beforeCreate => **setup()**
  - created => **setup()**
  - beforeMount => **onBeforeMount**
  - mounted => **onMounted**
  - beforeUpdate => **onBeforeUpdate**
  - updated => **onUpdated**
  - beforeUnmounted => **onBeforeUnmounted**
  - unmounted => **OnUnmounted**

- 组合式 API 的 vue2 生命钩子的**优先级：**

- - 下图打印结果可以看到：setup 中的组合式 API 优先级更高。

    ![image-20220810034139355](C:\Users\失频本人\Desktop\blog修改历史\blog\source_posts\image-20220810034139355-16600741018631.png)
