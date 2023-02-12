---
layout:
  - post
title: 关于移动端CSS
abbrlink: 7a4077d8
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - CSS
categories: CSS
date: 2021-12-11 21:33:56
img:
coverImg:
password:
summary:
---



**总结**： 兼容移动端主流浏览器，处理Webkit内核浏览器即可。

查看设备网站：https://material.io/devices/



## 1. 移动端调试方法

- chrome浏览器的模拟手机；
- 搭建本地服务器，手机和服务器在一个局域网内，通过手机访问服务器；
- 使用外网服务器，直接IP或域名访问。



## 2. 二倍图

### 2.1 物理像素&物理像素比

- PC端和古早的普通手机屏幕：1css像素=1物理像素。
- Retina（视网膜屏幕）是一种显示技术，可以将更多物理像素点压缩至一块屏幕里，从而达到更高分辨率，并提高屏幕显示的细腻程度。

所以：当一个50 * 50px的图片，直接放到iPhone8里就会被放大2倍至100 * 100导致模糊。

**解决**：采取使用100 * 100的图片，然后手动设为50 * 50的css像素。

**总结**：准备的图片比实际需要的大2倍，这种方式就是2倍图。



## 3. CSS初始化 normalize.css

- 保护了有价值的默认值
- 修复了浏览器的bug
- 是模块化的
- 有详细的文档。

**官网地址**： http://necolas.github.io/normalize.css/



## 4.移动端特殊样式

- 给a标签去掉高亮（移动端可能会有这种情况）：

```
  -webkit-tap-highlight-color: transparent;
```

​     

- 在移动端浏览器默认的外观在iOS上加上这个属性才能给按钮和输入框自定义样式：

```
-webkit-appearance: none;
```



- 禁止长按页面时的弹出菜单：

```
img, a {
  -webkit-touch-callout: none;
}
```

