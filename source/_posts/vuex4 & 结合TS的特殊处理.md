---
layout:
  - post
title: vuex4 & 结合TS的特殊处理
abbrlink: 2c9a25cc
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - TypeScript
  - vuex
  - vue3
categories:
  - TypeScript
  - vuex4
date: 2022-10-23 20:15:09
img:
coverImg:
password:
summary:
---



## 1. 关于vuex4

store/index.js文件：

1. 调用`createStore`创建仓库，它里面传入的是一个对象；
2. createStore会接收一个泛型，

```
// 先引入：
import { createStore } from 'vuex';

const store = createStore({
    state() {
        // 最终要返回个对象：
        return {
            // 
        }    
    }
    
})
```



### 1.1 在组件中使用：

**使用步骤**： 

1. 先引入vuex的`useStore`；

2. 获取到`useStore` ；

3. 调用其中的`dispatch`，同时传递**两个参数**：

4. 1. 什么操作；
   2. 传递的值。

```
import { useStore } from "vuex";

const store = useStore()；

// 调用dispatch，同时传递两个参数：什么操作、传递的值：
store.dispatch();
```



## 2. TS的相关支持

### 2.1 对useStore的处理

- vuex4版本对TS的支持不够完善，其中一点体现在`useStore`上。

- - 在组件中正常引入`useStore`使用时，state 是 any 类型也意味着这里缺少类型检测，所以在取store的子模块时会出现报错。
  - 例：

```
import { useStore } from "vuex";

const store = useStore();
const userMenu = store.state.login.userMenu;    // login报错
```



- **解决**：给`useStore`做了更改：

- - Step1. 在 store/index.ts文件：

```
/* 引入时顺便起别名因为自定义的也叫这个： */
//     其中Store是vuex提供的一个类型：
import { createStore, Store, useStore as useVuexStore } from "vuex"; 

export function useStore(): Store<IStoreType> {
  // 将vuex的useStore起别名后再return出去，而在这个过程中已经给它明确制定了类型：
  return useVuexStore();
}
```



- - Step2. 加类型，store/types.ts：

```
import { ILoginState } from "./modules/types";

export interface IRootStore {
  name: string;
  age: number;
}

// 定义root中的module的类型：
export interface IRootModule {
  login: ILoginState;
}

// 规定为交叉类型：
export type IStoreType = ILoginState & IRootModule;

```

- 之后在使用useStore时就使用自己处理过的👆🏻。



### 2.2 每个module的类型&传入的泛型

- 注意vuex4与TS类型的配合使用：

- 1. 首先**每个模块的类型是Module**；
  2. 而**Modules****要传入两个泛型**：**S和R**，（因为没有默认值）。

- - - **S指当前模块的state类型**；
    - **R指根模块中的类型**。 

源码处：

![image-20230214233106450](C:/Users/%E5%A4%B1%E9%A2%91%E6%9C%AC%E4%BA%BA/AppData/Roaming/Typora/typora-user-images/image-20230214233106450.png)



store/modules/login.ts：

```
import { Module } from "vuex"; // 为了与TS结合使用，使用vuex4时需要从vuex中引入Modules。
import { ILoginState } from "./types";
import { IRootStore } from "../types";

// 划分模块后，每个模块中Module都要有两个泛型，分别代表：当前模块和根模块的state类型。
const loginModule: Module<ILoginState, IRootStore> = {
  namespaced: true,
  state() {
    return {
      token: "",
      userInfo: {}
    };
  },
  getters: {},
  mutations: {},
  actions: {
    // action中的函数接收两个参数：上下文、value：
    accountLoginAction({ commit }, payload: any) {
      console.log("执行accountLoginAction", payload);
      // 1. 登录逻辑，发送请求：
    }
  }
};

export default loginModule;

```

