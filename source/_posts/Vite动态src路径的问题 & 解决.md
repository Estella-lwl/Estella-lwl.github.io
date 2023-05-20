---
layout:
  - post
title: Vite无法使用动态src路径的问题 & 解决
abbrlink: 6a3db6b3
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - Vite
categories: Vite
date: '2023-02-27 14:48'
img:
coverImg:
password:
summary:
---



### vite中使用动态图片路径src的三个方式：

#### 1. 使用 **new URL** 的方式；

#### **2.** 手动 import 引入每张图片；

#### 3. 使用以下方式👇🏻：

1. 将图片命名为统一前缀。

2. 使用Vite提供的 **import.meta.glob()**方法匹配文件。参考[Vite官方文档](https://vitejs.cn/vite3-cn/guide/features.html#glob-import)。

3. - import.meta.glob()：动态加载模块的方法，返回一个对象。例：**import.meta.glob("@/assets/imgs/pre_name\*.png")**
   - 使用通配符*****匹配；

4. 遍历匹配到的文件列表

5. - 通过对象的 key 值访问对应的模块；res为一个Module对象。
   - iconList.value[key] = res.default;   

将加载模块的default属性（路径）赋值给 iconList 对象的对应属性。



```
  // 匹配图片列表：
  const files = import.meta.glob("@/assets/imgs/game/game-project*.png");
  const iconList = ref<any>({});
  
  for (const key in files) {
    console.log("key: ", key);
    // 访问当前键名对应的文件模块 =》then回调将结果（路径）存入res中：
    files[key]().then((res: any) => {
      // 将匹配到的文件路径放入iconList：
      iconList.value[key] = res.default;
      console.log("iconList: ", iconList.value);
    });
  }
  
  // 返回一个对象键名（文件路径）组成的数组 & 遍历
  Object.keys(iconList.value).forEach((item) => {
    iconList.value[item]["namespaced"] = true;
    console.log("modules2222: ", iconList.value);
  });
```

**PS**：

- 官方提供new URL的方式，但打包后路径又会出问题。
- 新版本似乎会优化这个问题，最近没关注。