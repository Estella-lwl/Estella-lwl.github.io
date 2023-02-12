---
layout:
  - post
title: HTTP与Axios
abbrlink: d0bdcfbc
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - HTTP
  - Axios
categories: Axios
date: 2021-05-17 00:00:00
img:
coverImg:
password:
summary:
---



## 1.请求方式与请求参数

### 1.1 请求方式

1. **GET**：从服务器端读取数据      	 查（R）
2. **POST**：向服务器端添加新数据      	  增（C）
3. **PUT**：更新服务器端已存在的数据		改（U）
4. **DELETE**：删除服务器端的数据		删（D）

略...

### 1.2 请求参数：

#### 1.2.1 query参数（查询字符串参数）

- 参数包含在请求地址中，格式为 :/xxx?name=xxx&age=18 
- **敏感数据不要用query参数**，因为是地址的组成部分，比较危险。
- 备注：query参数又称**查询字符串参数**，编码方式为urlencoded。



#### 1.2.2 params参数

- 参数包含在请求地址中，格式为 http://localhost:3000/add_person/tom/188
- 敏感数据也不要params参数，原因同上。



#### 1.2.3 请求体参数

- 参数包含在请求体中，可以通过浏览器开发工具查看。

##### 1.2.3.1 请求体参数常用的两种格式：

- 格式1：**urlencoded格式**：
  - **例如**： name=tom&age=18  
  - 对应请求头： Content-Type: application/x-www-form-urlencoded
- 格式2：**json格式**：
  - **例如**： { "name": "tom", "age", 12 } 
  - 对应请求头： Content-Type: application/json
- **不同的格式必须要添加对应的请求头**。



**特别注意**：

1. GET请求不能携带请求体参数，因为GET请求没有请求体。
2. 理论上一次请求可以随意使用上述3种类型参数的任何一种，甚至一次请求的3个参数可以用3种形式携带，但一般不会这样。
3. 有一些约定俗成的**规则**：
   1. 例如form表单发送post请求时：自动使用请求体参数，用urlencoded编码。
   2. 例如jQuery发送ajax-post请求时：自动使用请求体参数，用urlencoded编码（因为这种编码方式转换比较简单）。





------

## 2. API分类

### 2.1 REST API(restful 风格的API)

1. 用请求方式来决定是CRUD中的哪个操作。
2. 同一个请求路径可以进行多个操作。
3. 请求方式会用到GET/POST/PUT/DELETE



### **2.2 非REST API(restless 风格的API)**

1. 请求方式不决定请求的CRUD操作
2. 一个请求路径只对应一个操作
3. 一般只有GET/POST





------

## 3. 使用json-server的使用搭建REST API

- json-server：用来快速搭建**REST API**的工具包。
- 可以用来模拟数据。
- 注意：接收不到query参数。视频讲解：https://www.bilibili.com/video/BV1Ga41177Tn?p=5&t=1181.9



### 3.1 使用

GitHub：https://github.com/typicode/json-server

**步骤：**

- **全局安装**：npm i json-server -g
- **启动**：
  -  json-server xxx.json  ， 其中xxx代表任意名称。这行命令会**自动创建**json文件，该文件代表新建的数据库json文件。
  - 完整写法： json-server --watch xx.json  
  - 可以**在任意文件夹执行命令**。
  - 已解决跨域。





------

## 4. postman接口测试工具

下载：https://www.postman.com/downloads/

其余略...





------

## 5. http请求与ajax请求

1. ajax请求是**一种特别的http请求**。
2. 对服务器端来说，没有任何区别，区别在浏览器端。
3. 浏览器端发请求：只有XHR或fetch发出的才是Ajax请求，其他所有的都是非ajax请求。
4. 浏览器端接收到响应：
   1. **一般请求**：浏览器一般会直接显示响应体数据，也就是常说的自动刷新/跳转页面。
   2. **ajax请求**：浏览器不会对界面进行任何更新操作（页面无刷新），只是调用监视的回调函数并传入响应相关数据。
