---
layout:
  - post
title: reactive不能解构
abbrlink: 263d655f
author: polariis
top: true
cover: false
toc: false
mathjax: false
tags:
  - Vue3
categories: Axios
date: 2022-07-09 00:00:00
img:
coverImg:
password:
summary:
---



- 当使用reactive定义一个对象，不能使用解构（**数据不能修改成功**）。

  - 举例：

    ```
    const obj = reactive({
        a: 'polaris',
        b: 20
    })
    
    obj.b = 13;
    console.log(obj.b); // b=20，未改成功。
    ```

- - **解决**： 使用toRefs。
  - **注意**：使用了toRef或toRefs，取值要.value。

```
// 修改后：
const obj = reactive({
    a: 'polaris',
    b: 20
})

obj.b = 13;
console.log(obj.b); // b=20，未改成功。
```

