---
layout:
  - post
title: axios封装
abbrlink: 876a38c0
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - Axios
  - 更新中
categories: Axios
date: 2022-02-26 00:00:00
img:
coverImg:
password:
summary:
---

​    

## 1. axios全局配置

- 有些信息是重复的，比如：baseUrl、header、超时时间。

```
axios({
    baseURL: 'http://xxx',
    timeout: 5000,
    url: '/home/multidata'
})
```



- 所以这些重复的可以写为全局配置：

```
axios.defaults.baseURL= 'http://xxx'
axios.defaults.timeout= 5000
axios({
    url: '/home/multidata'
})

```

​          

**常见配置选项：**

 <img src="https://s2.loli.net/2022/10/05/l5DkjCtx6df3uhL.png" alt="image-20221005170242319" style="zoom:67%;" />



## 2. axios的实例和模块封装

### 2.1 创建实例

当有多个服务器地址时，配置会不一样，所以每个都创建一个实例互不干扰。

例：

```
// 创建实例：
const instance1 = axios.create({
    baseURL: 'http://xxx',
    timeout: 5000
})

instance1({
    url: '',
    params: {
        page: 1
    }
})
```

  

### 2.2 axios模块封装（回调函数的方式，关于promise方式这里未记录）

**Step1**. 创建network文件夹，新建request.js文件：

```
// 引入axios:
import axios from 'axios';

// 多传递两个参数: success成功和failure失败，而它们刚好是函数：
export function request(config, success, failure) {
    // 创建axios实例：
    const instance = axios.create({
        baseURL: '',
        timout: 5000
    })
    
    // 发送真正的网络请求：
    instance(config).then(res => {
        // 成功就调用success函数并传值：
        success(res);
    }).catch(err => {
        failure(err);
    })
}
```

**Step2**. 使用：（我先放在了main.js中）

```
// 先引入方法：
import { request }from '@network/request';

request({
    // config配置：
    url: '/home/multidata' 
    // 成功回调：
}, res => {
    // 这里拿到成功结果：
    console.log(res);
}, err => {
    console.log(err);
})
```



### **2.3 上段封装的代码简写**

- 相当于只有一个参数，之前的三个参数以对象属性的方式放进了这一个大的对象里。   

```
export function request(config) {
    // 1. 创建axios实例:
    const instance = axios.create({
        baseURL: 'http://123/207.32.32:8000',
        timout: 5000
    })
    
    // 发送真正的网络请求：
    return instance(config);
}
```

别的js文件引用后：

```
request({
    url: './home/multidata'
}).then(res => {
    console.log(res);
}).catch(err => {
    console.log(err);
})
```



下面这种暂时别看：

```
export function request(config) {
    // 1. 创建axios实例:
    const instance = axios.create({
        baseURL: 'http://123/207.32.32:8000',
        timout: 5000
    })
    
    // 发送真正的网络请求：
    instance(config.baseConfig).then(res => {
        config.success(res);
    }).catch(err => {
        config.failure(err);
    });
}
```

​           

```
// 别的js文件调用：
request({
    baseConfig: {
        
    },
    success: function(res) {
        
    },
    failure: function(err) {
        
    }
})
```





### 2.4 关于封装的补充说明

- 关于本篇笔记**2.2小节**两个步骤的代码块中回调的理解。

**举个例子**：

```
// 这样写函数直接就执行完了，没有回调：
function test() {
}
test();
```

​           

- 所以改成test函数的参数是函数的形式：

```
// 现在要拿到成功和失败的回调，所以需要函数在适当的时候被调用来触发操作（想象vue父子组件传值中的通过函数传递的方法，就是通过其参数也是函数的形式。）
function test(aaa, bbb) {
    // 调用函数aaa，触发执行调用aaa函数体：	
    aaa();
}

// 调用test函数
test(function() {
     // 这里面就相当于aaa函数：
}, function() {
    // 这里面就相当于bbb函数：
})
```

​            

- 加上打印后看下顺序：（33， 11， 44， 22）

```
    // 声明test，此时函数体里未执行：
    function test(aaa, bbb) {
      // 第2个打印：
      console.log(11);
      aaa();
      // 最后一个打印，因为被aaa的调用插了队。
      console.log(22);
    }
    
    // 第一个打印
    console.log(33);             
    // 看到有调用，就立马去执行test函数体里的代码。然后看到了调用aaa，同理立马去找aaa。
    test(function () {
      // 这里是aaa函数，所以被上面test里调用后立马找到这里执行。第3个打印：
      console.log(44);
    }, function () {
      // 55没被打印是因为没调用bbb:
      console.log(55);
    })
```



- 在上面的基础上，**回归正题**：

```
function test(aaa, bbb) {
    // 在这里回调的时候就把值传过去了：
    aaa('我是res的实参');
    bbb('我是err的实参')
}

test(function(res) {
    // 这时就可以拿到aaa被调用时传过来的值：
    console.log(res);           //  结果为： 我是res的实参
}, function(err) {
    console.log(err);
})
```



- 所以基于以上，**2.2小节**中**封装axios是一样的思想**，**只不过分开放在了两个js文件中&增加了导入导出。**

（视频讲解：https://www.bilibili.com/video/BV15741177Eh?p=147&t=234.1）

另外关于**结合函数柯里化**的封装方式：[JS函数柯里化](note://BF135A654F4D410C8D4522384737DFC2)

------

整理后的版本：

- 参考自：https://www.bilibili.com/video/BV1QA411b7TR?p=7&vd_source=d839eda9a2b875da47ce4ee8814c818f

​                              

1.

<img src="https://s2.loli.net/2022/10/05/rl4mQRtgE2HyzFv.png" alt="image-20221005170856416" style="zoom: 80%;" />

2.

   <img src="https://s2.loli.net/2022/10/05/mOgRowl9udF1x7B.png" alt="image-20221005170948944" style="zoom:80%;" />

通过config，统一res，err。



3.通过promise

<img src="https://s2.loli.net/2022/10/05/htNxGyLMcnoCzde.png" alt="image-png" style="zoom: 80%;" />



4.axios本事返回就是promise，所以直接把第三种简写为

<img src="https://s2.loli.net/2022/10/05/D8JOdSoVflLmP6E.png" alt="image-png" style="zoom: 80%;" />
