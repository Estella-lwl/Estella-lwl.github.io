---
layout:
  - post
title: JS深拷贝
abbrlink: 3136a465
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - JavaScript
  - 更新中
categories: JavaScript
date: 2022-08-17 21:27:54
img:
coverImg:
password:
summary:
---



拷贝的对象可能是多层级的，要确保深层嵌套也一样，可以使用递归，所以：

- 如果是原始类型 =》 直接返回，无需继续拷贝。
- 如果是引用类型 =》 创建新对象，遍历要克隆的对象&将对象属性深拷贝后依次放入新对象中。

##### 完善过程：（<span style="color: #3850b8">最终版拉到最后</span>）

1. **未考虑数组的情况下**：

```
export function basicClone(target) {
  // 先判断是否为对象类型：
  if (typeof target === 'object') {
    let cloneTarget = {};
    for (const key in target) {
      // 将原对象中每一项放入新对象：
      cloneTarget[key] = basicClone(target[key]);
    }
    // 返回新对象：
    return cloneTarget;
  } else {
    return target;
  }
}
```



2. **根据以上改进加入数组**：

```
export function Clone(target) {
  if (typeof target === 'object') {
    // 关键：如果target是数组则定义个新数组而非对象。其余代码与上种无差别。
    let cloneTarget = Array.isArray(target) ? [] : {};
    for (const key in target) {
      cloneTarget[key] = Clone(target[key]);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```



3. **考虑栈溢出的情况**：(当对象中的属性引用了自身，进入死循环会导致栈溢出。)

1. 1. <span style="color: #0091ff;background-color: #f2f4f5;">解决循环引用</span>：开辟新的存储空间放置当前对象和拷贝对象的对应关系。

   2. 1. 当需要拷贝当前对象时先去存储空间找。有拷贝过这个对象 =》直接返回；
      2. 没有的话 =》继续拷贝。

   3. Map数据结构：https://juejin.cn/post/7064479924757168165。

```
// 测试用例：
const target = {
  field1: 1,
  field2: undefined,
  field3: {
      child: 'child'
  },
  field4: [2, 4, 8]
};
target.target = target;  // 这里导致栈溢出。


export function deepClone(target, map = new Map) {
  if (typeof target === 'object') {
    let cloneTarget = Array.isArray(target) ? [] : {};
    // 判断map中有没有已经克隆过的对象：
    if (map.get(target)) {
      // 有 =》直接返回：
      return map.get(target);
    }
    // 没有 =》将当前对象作为key，克隆对象作为value进行存储：
    map.set(target, cloneTarget);
    // 继续克隆：
    for (const key in target) {
      cloneTarget[key] = deepClone(target[key], map);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

待完善，

关于WeakMap及其它拓展暂未学习。

参考：https://juejin.cn/post/6844903929705136141
