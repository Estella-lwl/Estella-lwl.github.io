---
title: ref & reactive & 响应式差异
abbrlink: 9badfa3f
author: polariis
top: true
cover: false
toc: true
mathjax: false
categories: Vue3
tags:
  - Vue3
date:
img:
coverImg:
password:
summary:
---



## 1. Ref

- **作用**：定义一个响应式的数据。

- **语法**：const xxx = ref(initValue)  

- - 创建一个包含响应式数据的引用对象（reference对象）。
  - 模板中使用引用对象不用后面加.value，而setup中使用该值时需要加.value。

- **接收的数据**：

- - 可以是：基本类型、对象类型。
  - ref处理 **基本类型： 响应式**依旧使用是**defineProperty** ，以set & get数据劫持的方式。
  - 而处理 **对象类型**时使用**proxy**实现的（proxy是封装在reactive函数中的，ref内部会自动通过reactive转为代理对象）。
  - 补充：使用ref数据要 .value 是因为Object.defineProperty本身无法作用于基本数据类型，所以通过对象做了一层转化。

- **使用ref获取节点**：

- **注意**：如果要在setup中拿到DOM元素，声明的变量名必须与元素中写的ref**一致**。

<template>  <div id="countBox" ref="countupRef">    <span class="countSpan"></span>  </div> </template>

- 在**onMounted**中可以拿到节点：

  ```
    setup() {
        // 这里的变量名countupRef必须与ref写的一样：
      const countupRef = ref(null);
      console.log("ref", countupRef);
  
      onMounted(() => {
        // 拿到ref节点
        console.log(countupRef);
      });
  
      return {
        countupRef
      };
    },
  ```
  
  



## **2. reactive：**

- **作用**：定义一个**对象/数组类型**的响应式数据（基本类型别用，要用ref函数）。

- **语法**：const 代理对象 = reactive(源对象);  接收一个对象或数组，返回一个**代理对象**（Proxy的实例对象，简称proxy对象）。

- - 绿色框里的就是源对象，粉色的是代理对象：

 ![image-20220810035136457](C:\Users\失频本人\Desktop\blog修改历史\blog\source\_posts\image-20220810035136457-16600746990031.png)

- reactive定义的响应式数据是”深层次的“。
- 内部基于ES6的Proxy实现，通过代理对象操作源对象内部的数据。

## **3. ref对比reactive总结**

- **从定义数据据角度对比**：

- - ref定义：基本类型数据。
  - reactive定义：对象/数组类型数据。
  - 注意：ref也可以定义对象/数组类型数据，它内部会自动通过reactive转为代理对象。

- **从原理角度对比**：

- - ref：通过Object.defineProperty()的get和set实现响应式（数据劫持）。
  - reactive：通过使用Proxy实现响应式（数据劫持），并通过Reflect操作源对象内部的数据。

- **从使用角度对比**：

- - ref定义的数据：操作数据需要.value，读取数据时模板会直接读取不用.value。
  - reactive定义的数据：操作数据与读取数据均不需要.value。

## **4. 响应式原理对比**

## **4.1 Vue2的响应式**

- **实现原理**：

- - **对象类型**：通过**Object.defineProperty()** 对属性的读取、修改进行拦截（**数据劫持**）。

  - **数组类型**：通过**重写更新数组的方法实现拦截**。

  - - 比如：调用push方法，其实调用的是vue2二次封装后的push。

    - 这个push里做了两件事：

    - - \1. 帮助正常调用push方法。
      - \2. 帮助更新界面。

- **存在的问题**：

- - 新增属性 or 删除属性，**界面不会自动更新**。
  - 直接通过下标修改数组，界面也不会自动更新。

```
// 修改对象：
//     添加属性：
this.$set(target, 'name', value);
Vue.set(target, 'name', value);
// 删除属性：
this.$delete(target, 'name', value);
Vue.delete(target, 'name', value);

// 修改数组：
this.xxx.splice(0, 1, '');
```

![image-20220810035246493](C:\Users\失频本人\Desktop\blog修改历史\blog\source\_posts\image-20220810035246493-16600747685122.png)

- **Object.defineProperty**:

- - 读取和修改有**get**和**set**方法，但是添加和删除是捕获不到的。
  - **configurable**配置项：能否使用delete删除属性，默认值为false。

### **4.2 Vue3的响应式**

#### **4.2.1实现原理：**

- 通过**Proxy**（代理）：拦截对象中任意属性的变化，**包括**：属性的读写 、添加、删除 等。

- 通过**Reflect**（反射）：对源对象（被代理）的属性进行操作。

- MDN文档中描述的**Proxy**与**Reflect**：

- - **Proxy**：window上内置的，接收**两个参数**（必填）：**1**.target(代理对谁的操作)；**2.** 对象，至少要有个空对象占位。
  - **Reflect**：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

- vue2和vue3响应时原理对比的补充：https://juejin.cn/post/7001999813344493581

- - **模拟Vue3中实现响应式**：（不完整，看下面的**reflect**。）

```
		// 模拟Vue3中实现响应式：
		const p = new Proxy(person,  {
    // 读取某个属性时调用此方法：接收两个参数：源对象&当前属性名
    get(target, propName) {
        console.log(`${propName}属性被读取~`);
        return target[pcropName];    
    },
    // 修改/增加某个属性时调用此方法：接收两个参数：比get多收到一个value值。
    set(target, propName, value) {
        console.log(`${propName}属性被修改，准备更新界面`);
        target[propName] = value;    
    },
    // 删除某个属性时调用此方法：
    deleteProperty(target, propName) {
        console.log(`${propName}属性被修改，准备更新界面`);
        // delete target[propName]  最好是把真实结果给他返回：
        return delete target[propName]   
    }
})
```

绿色框在真正修改源数据：

![image-20220810035353361](C:\Users\失频本人\Desktop\blog修改历史\blog\source\_posts\image-20220810035353361-16600748348953.png)

#### 4.2.2. 响应式中Reflect的应用：

- **ES6**，**Windows内置**。
- 增删改查**用法**：reflect.get、reflect.set、 reflect.delete。
- reflect里也有defineProperty方法，且有返回值（若不返回的话就不知道是否操作成功，因为它不报错。返回值是布尔值）。

**与Object.definProperty****对比****：**

- **在做封装的时候，Object.definProperty捕获错误只能通过try catch。若使用reflect就可以避免。**https://www.bilibili.com/video/BV1Zy4y1K7SH?p=147&t=672.1

-  **模拟Vue3中实现响应式**（使用**reflect**改进后）：

- - 用到了proxy代理对象，及reflect反射对象。

    ```
    		// 模拟Vue3中实现响应式：
    		const p = new Proxy(person,  {
        // 读取某个属性时调用此方法：接收两个参数：源对象&当前属性名
        get(target, propName) {
            console.log(`${propName}属性被读取~`);
            return Reflect.get(target, propName);    
        },
        // 修改/增加某个属性时调用此方法：接收两个参数：比get多收到一个value值。
        set(target, propName, value) {
            console.log(`${propName}属性被修改，准备更新界面`);
            Reflect.set(target, propName, value);    
        },
        // 删除某个属性时调用此方法：
        deleteProperty(target, propName) {
            console.log(`${propName}属性被修改，准备更新界面`);
            // delete target[propName]  最好是把真是结果给他返回：
            return Reflect.deleteProperty(target, propName); 
        }
    })
    ```

    

------



**toRef**

- toRef可以创建一个ref，其的value值可以指向令一个对象的属性。

**举例**：

```
const obj = {
    a: 10,
    b: 'start'
}

const obj2 = toRef(obj)

return {
    obj,
    // 返回的格式：
    ...toRef(obj)
}
```

**toRefs**

指向多个

**toRefs**

指向多个属性
