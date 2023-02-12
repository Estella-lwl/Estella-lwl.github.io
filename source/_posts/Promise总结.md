---
layout:
  - post
title: Promise总结
abbrlink: 5b39b356
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - JavaScript
  - 更新中
categories: JavaScript
date: 2022-02-27 10:52:38
img:
coverImg:
password:
summary:
---



**关联笔记**：[Promise补充](note://WEBcaea4312c824c3e5ed720687a4dde45f)    [事件循环event loop](note://WEBb2bc4bcd1fe230dba73429e31b71de72)

------



## 1. 一些需要知道的前置内容

### 1.1 实例对象与函数对象

- <span style="color: #ff0001">实例对象</span>：new函数产生的对象，称为实例对象，简称对象。
- <span style="color: #ff0001">函数对象</span>：将函数作为对象使用时，简称函数对象。

看以下对比：

```
 function Fn() {}            // Fn是函数             
```

```
 const Fn = new Fn();        // Fn是构造函数；fn是实例对象。
```

```
console.log(Fn.prototype);  // Fn函数对象                 
```

```
Fn.call({})                 // Fn函数对象  
```

```
$('#test')                  // Jquery函数            
```

```
$.get('/test')              // Jquery函数对象
```

​              

异步加载图片为例：

```
// 声明一个函数&传3个参数：
function loadImg(src, res, reject) {
    // new一个对象
    let img = new Image();
    // 增加3个对象属性&赋值：
    img.src = src;
    img.onload = res;
    img.onerr = reject;
}
// 调用loadImg
loadImg({})
```

​       通过回调，



### 1.2 两种回调函数（同步&异步）

#### 1.2.1 同步回调函数：

- 1. **理解**：<span style="color: #ff0001">立即执行，每一步完全执行完才结束</span>，不会放进回调队列中。
- 2. **例子**：看下打印出来的顺序

```
const arr =  [1, 2, 3];
arr.forEach(item => {       // 遍历回调；同步回调函数。不会放入队列，一上来就执行。
    console.log(item);
})

console.log('forEach()后的打印');
```

​            

- 打印**结果**：arr中的每个item被打印，然后是第5行。
- **说明**：这段代码同步的。函数内每一步都执行完了才会进行下面的代码。

#### 1.2.2 异步回调函数：

- 1. **理解**：不会立即执行，而是<span style="color: #ff0001">放入回调队列等待执行</span>。
- 2. **例子**：（比如：定时器回调、ajax回调、Promise的成功or失败回调。）

```
// 异步回调函数，会放入队列在将来执行。
setTimeout(() => {
    console.log('我是setTimeout中的回调~');
}, 0)

console.log('我是setTimeout后的打印');       // 先打印
```

​        

- 打印**结果&说明**：这段代码会先打印第4行，尽管定时器时间为0。因为是异步的，定时器里的需要等待后执行里面的打印；所以第4行直接先被打印。

- 1. 如果要判断一个函数是否是异步的，可以打印输出看顺序**看是否需要等待**输出来判断。
- 2. 并不是所有回调都是异步。



### 1.3 常见的内置错误

#### 1.3.1 错误的类型：

- **Error:** 是以下所有错误的<span style="background-color: #f2f4f5;">父类型</span>。
  - **1.** <span style="color: #CC0000">**ReferenceError**</span>：<u>引用的变量不存在</u>；

```
// 例：
console.log(a);    // ReferenceError: a is not defined

console.log('====');    // 未捕获错误，本行代码也不会执行。
```

- - **2.** <span style="color: #CC0000">**TypeError**</span>： <u>数据类型不正确</u>；

```
// 例：
let b = undefined;
b.xxx();             // Uncaught TypeError: b.xxx is not a function

// 或：
console.log(b.xxx);  // Uncaught TypeError: cannot read property 'xxx' of undefined

```

- - **3.** <span style="color: #CC0000">**RangeError**</span>： <u>数据值不在其所允许的范围内</u>；

    ```
    // 例：
    function fn () {
        // 在函数内部调用自己:（叫递归调用）
        fn();
    }
    
    // 加上这行就会报错：（最大调用栈溢出，也就是数据值不在其所允许的范围内）
    fn();              // RangeError: Maximum call stack size exceeded
    ```

      

    ​    **PS**：**这种形式不叫函数自调用，要区分开**。函数自调用是：一次性函数。  https://www.cnblogs.com/liushisaonian/p/9425427.html

  - **4.**  <span style="color: #CC0000">**SyntaxError**</span>： <u>语法错误</u>。

    ```
    // 例：
    // 本意：字符串里放入一个空字符串
    const c = """";       // SyntaxError: Unexpected string     
    ```

    

#### 1.3.2 错误处理：

不处理程序没法向下执行，有时需要在出现错误时加一些提示，所以会处理。

- **1.** <span style="color: #CC0000">**捕获处理**</span>：**try  ...  catch**
  - **error里面默认有两个属性：<span style="color: #0091ff">1.</span> message 信息 ；<span style="color: #0091ff">2.</span> stack 调用栈**	

```
//
try {
    let d;
    console.log(d.xxx);
} catch (error) {
    console.log(error.message);   // 提示文本
    console.log(error.stack);     // 相关信息文本
}

// 只有上面处理了错误后，下面这行才可以正常执行：
console.log('上面出错了');
```

​             

- **2.** <span style="color: #CC0000">**抛出错误**</span>：**throw error**	
  - 当想让调用者自己处理，就可以使用抛出异常。若没有相关处理的需求也就没必要抛出异常了。
  - 当自己抛异常时通常抛的是**Error**类型，而不是其下的别的类型错误。

```
// 抛出错误： throw
function fn() {
    // 判断当前时间是否为奇数:
    if(Date.now()%2 === 1) {
        console.log('是奇数，可以执行任务');
    } else {
        // 抛出异常，由调用者来处理：
        // new出来一个Error，它是对象类型。里面参数是错误信息
        thorw new Error('当前为偶数，无法执行');
    }
}

// 当调用时，要先捕获(否则不执行):
try {
    fn();
} catch (err) {
    alert(err.message);   // 给个错误提示的效果
}

```

​              

------

## 2. Promise

### 2.1 Promise的理解

- Promise是JS中进行异步编程的新的解决方案（旧方法：纯回调）。
- 具体来讲：
  - **语法上**：Promise是一个构造函数。
  - **功能上**：Promise对象用来封装一个异步操作并可以获取其结果。

### 2.2 promise的状态改变

- pending变为resolved；
- pending变为rejected。

PS：

- **只有这2种**。且一个promise对象只能改变一次。
- 成功&失败都会有一个结果数据。
- 成功的结果数据称为value，失败的结果数据称为reason。





------

未完待续.......

### 2.3 promise的基本流

### 2.4 promise的基本使用

### 2.5 为什么使用promise

### 2.6 如何使用promise

------

## 3. 手写promise

------

## 4. async & await

补充：https://zhuanlan.zhihu.com/p/26260061

------

## 5. JS异步——宏队列和微队列

完整笔记：[事件循环event loop](note://WEBb2bc4bcd1fe230dba73429e31b71de72)

------

## 6. promise练习题
