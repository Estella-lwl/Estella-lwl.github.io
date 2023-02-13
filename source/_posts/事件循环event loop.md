---
layout:
  - post
title: 事件循环event loop
abbrlink: 61ebbe5a
author: polariis
top: true
cover: true
toc: true
mathjax: false
tags:
  - JavaScript
  - 更新中
categories: JavaScript
date: 2022-05-06 00:00:00
img:
coverImg:
password:
summary:
---

- 从浏览器多进程到 JS 单线程，JS 运行机制最全面的一次梳理：https://segmentfault.com/a/1190000012925872#articleHeader17
- 参考自：晓舟的系列视频 [前端面试题：JavaScript 运行机制（一）单线程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1gB4y1K7bD?share_source=copy_web&vd_source=72dc2b34128eeca7f0729ade125cd412)

---

## 1. event loop 事件循环

- JavaScript 从一开始就是<span style="color: #0091ff">单线程</span>，**原因**：这与它的<span style="color: #0091ff">v</span>有关，JavaScript 作为一个浏览器脚本语言，<span style="color: #0091ff">偏向于用户侧的交互，为了降低复杂性</span>，这决定了它只能是单线程，否则会带来很复杂的同步问题。
- HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。

### 1.1 同步 & 异步：

<span style="color: #3850b8">**场景**</span>：当遇到一个非常耗时的任务，而这时又需要响应用户的操作。为了避免页面出现“假死”的状态，**避免主线程的阻塞**，JavaScript 有了 **同步** 和 **异步** 的概念。

<span style="color: #0091ff">**同步任务**</span>：在<span style="color: #ff0001">**主线程**</span>上的<span style="color: #ff0001">依次执行</span>任务。

<span style="color: #0091ff">**异步任务**</span>：<span style="color: #ff0001">不进入主进程</span>，<span style="color: #ff0001">**在任务队列**</span>（task queue）的任务。只有等待主线程空闲的时候（调用栈被清空），该任务才会进入主线程执行。

举例：

```
 console.log(0)
 setTimeout(function() {
     console.log(1)
 }, 1000);
 console.log(2)
 // 打印顺序是 0 2 1
```

这段代码中：

- 主线程首先打印 0，看到 setTimeout 后处理为异步，1s 过后 setTimeout 的回调函数会进入任务队列。
- 主线程继续运行，打印 2。
- 在主线程的任务运行完成之后，会再轮询任务队列，打印出 1。

### 1.2 任务队列：

- 单线程就意味着所有任务需要排队。如果前一个任务耗时很长，后一个任务就等着。
- 很多时候 CPU 是闲着的，因为 IO 设备很慢（**比如**Ajax 操作从网络读取数据），必须要等着结果出来，再往下执行。所以 JavaScript 语言的设计者意识到，这时主线程完全可以不管 IO 设备，挂起处于等待中的任务，先运行排在后面的任务。等到 IO 设备返回了结果，再回过头，把挂起的任务继续执行下去。
- （ I/O 设备就是可以将数据输入到计算机，或者可以接收计算机输出数据的外部设备，属于计算机中硬件部件。）

### 1.3 执行流程 & 图解：

1. 所有同步任务都在<span style="color: #3850b8">主线程</span>上执行，形成一个<span style="color: #3850b8">**执行栈**</span>（execution context stack）。
2. <span style="color: #3850b8">主线程</span>之外，还存在一个“<span style="color: #3850b8">**任务队列**</span>”（task queue）。只要异步任务有了运行结果，就在“任务队列”之中放置一个事件。
3. 一旦“执行栈”中的所有同步任务执行完毕，系统就会读取“任务队列”，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。

- 主线程从任务队列中读取事件,这个过程是**不断循环**的,所以整个的运行机制称为<span style="color: #0091ff">**event loop**</span>。
- Node.js 的 Event Loop：略。

**参照下图**：

<img src="https://s2.loli.net/2022/10/05/ETdfwMcaNrmpxCH.png" alt="image-20221005152751007" style="zoom: 50%;" />

### 1.4 事件循环的三种类型：

1. <span style="color: #3850b8">Window 事件循环</span>

2. <span style="color: #3850b8">Worker 事件循环</span>

3. <span style="color: #3850b8">Worklet 事件循环</span>

> 多个同源窗口可能运行在相同的事件循环中，每个队列任务进入到事件循环中以便处理器能够轮流对它们进行处理。
>
> **PS**：
>
> 此处同源指由同一个窗口打开的多个子窗口或同一个窗口中的多个 iframe 等，意味着起源的意思。
>
> “window” 指用于运行网页内容的浏览器级容器，包括实际的 window，一个 tab 标签或者一个 frame。
>
> 在特定情况下，**同源窗口之间共享事件循环**，例如：
>
> - 如果窗口是包含在 iframe 标签中，则它可能会和包含它的窗口共享一个事件循环。
> - 在多进程浏览器中多个窗口碰巧共享了同一个进程。
> - 具体细节可能因浏览器而异，取决于它们的实现方式。

## **2. 宏任务 &** 微任务：

- <span style="color: #3850b8">**异步任务队列**</span>：<span style="color: #0091ff">**宏任务**</span>(macro-task)和<span style="color: #0091ff">**微任务**</span>(micro-task)。
- **执行顺序**：执行栈在<span style="color: #3850b8;background-color: #f2f4f5;">执行完同步任务后，查看执行栈是否为空，如果执行栈为空</span> =》执行宏任务。<span style="color: #3850b8">每次</span>宏任务执行完毕后，检查微任务队列是否为空，如果不为空 =》按照<span style="color: #3850b8">先入先出</span>的规则全部执行完微任务 =》设置微任务队列为 null =》再执行宏任务，如此循环。

> <span style="color: #CC0000">**宏任务队列可以有多个，微任务队列只有一个。**</span>

**注意**：<span style="color: #3850b8;background-color: #f2f4f5;">当**宏任务**出队时，任务是一个个执行</span>；而<span style="color: #3850b8;background-color: #f2f4f5;">**微任务**出队时，任务是一队一队执行</span>。因此处理微任务队列这一步，会逐个执行队列中的任务并把它出队，直到队列被清空。

**宏任务主要包含**：<span style="color: #0091ff">script( 整体代码)、setTimeout、setInterval、 I/O、UI 交互事件</span>......

**微任务主要包含**：<span style="color: #0091ff">Promise、process.nextTick</span>(Node.js 环境)......

如图：

<img src="https://s2.loli.net/2022/10/05/pjJeRPY6vWdtKh2.png" alt="image.png" style="zoom:50%;" />

**例题**：

```
 setTimeout(function() {
     console.log('1');
 })

 new Promise(function(resolve) {
     console.log('2');
 }).then(function() {
     console.log('3');
 })

 console.log('4');
```

- **打印顺序**：2 4 3 1
- **原因**：

- - 1.
  - 2.

## 3. Promise

这部分内容主要在：[Promise](note://E95A3D8DBC074A43AC8F6F9C15FFE2D5)
