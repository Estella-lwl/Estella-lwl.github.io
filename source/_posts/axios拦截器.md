---
layout:
  - post
title: axios拦截器
abbrlink: 9ccbc75b
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - Axios
  - 更新中
categories: Axios
date: 2021-07-17 00:00:00
img:
coverImg:
password:
summary:
---



## 1. 请求拦截器

- **axios.interceptors.request.use();**
- **axios请求拦截器**：是发出请求前执行的一个**回调函数**。
- **作用**：对所有请求做统一处理：追加请求头、追加参数、界面loading提示。
- 所以可以写为以下方式：
  - 一般会在这里带个**token**。

```
axios.interceptors.request.use((config) => {
    console.log('config', config);
    
    // 判断身份信息：
    if (store.getters.token) {
      // 让每个请求携带token--['X-Token']为自定义key：  请根据实际情况自行修改。
      config.headers['X-Token'] = getToken(); 
    }
    // 经过拦截检查后，再把config还回来：
    return config;
});
```



- 请求发送出去之前，会经过请求拦截器。所有配置都交给了请求拦截器里的函数。
  - 把所有请求的信息打包成一个config对象。
  - 检查过后会把config再给回来。



## 2. 响应拦截器

- **axios.interceptors.response.use();**
- **axios响应拦截器**：得到响应后执行的回调函数。
- 请求成功：对成功的数据进行处理使用。
- 请求失败：对失败统一的操作。
  - **什么时候调error**：只要状态码不是2开头的，就都被认作失败。
  - 需要返回一个失败的Promise实例（**注意**：除了Promise之外的返回，都被认定为成功，所以这里必须返回Promise）。
  - 可以直接在这里做整体的状态码提示，这样不用后续所有响应中中些error，因为在拦截时就已经可以统一处理 & 弹出提示了。
- PS：不同的是，响应拦截器的两个函数：response、 error。
- 在返回的所有数据中只有data是后续需要的，所以直接 return response.data 

```
axios.interceptors.response.use(
    // 成功回调：
    (response) => {
        console.log('response成功的回调', response);
        // 同样需要return：
        return response.data;
    },
    
    // 失败回调：
    (error) => {
      console.log('error失败的回调', error); 
      alert(error);
      // 失败时需要返回Promise实例：
      return Promise.reject(error);
    }
);

// 前几行写法的好处体现在直接可以这样写：
    // 
btn.onclick = async() => {
    const result = await axios.get('请求地址');
}
```

**另外**：

- 可以写多个拦截器。
- 有多个时：先指定的后执行。
- 可以参考PanJiaChen写的后台文档总结：https://juejin.cn/post/6844903476661583880



关联笔记：
[权限控制]()
