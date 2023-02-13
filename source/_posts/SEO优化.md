---
layout:
  - post
title: SEO优化
abbrlink: 7f8d26b3
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - SEO
date: 2021-12-18 11:21:35
categories:
img:
coverImg:
password:
summary:
---

## 1. SEO

**1. SEO：Search Engine Optimization，译为 搜索引擎优化，是一种利用搜索引擎的规则提高网站在有关搜索引擎内自然排名的方式。**

- SEO 的**目的**是：对网站进行深度优化，从而帮助网站获取免费的流量，进而在搜索引擎上提高网站的排名。

- 页面必须有三个标签用来符合 SEO 优化。

- - **title 网站标题**

  - - title 具有不可代替性，是内页的第一个重要标签，是搜索引擎了解王爷的入口和对网页主题归属的最佳判断点。
    - 建议写法：<span style="color: red">网站名（产品名）-网站的介绍</span>（尽量不要超过 30 个汉字）

  - **description 网站说明**

  - - <span style="color: red">简要说明网站是干什么的。 </span>
    - 例：京东 JD.COM-专业的综合网上购物商城，销售家电、数码通讯、电脑、家居百货.......为您提供愉悦的购物体验。

  - **keyword 关键字**

  - - 页面关键字，是搜索引擎的关注度之一。
    - 最好限制在 6-8 个关键字，关键字之间用英文逗号隔开。

  - 例：

- ```
  <meta name="description" content="......"/>
  ```

-

## 2. LOGO 的 SEO 优化：

具体步骤：

**1. logo 里面首先放一个 h1 标签，目的是为了提权，告诉搜索引擎这个地方很重要。**

**2. h1 里面再放一个链接(a)，可以返回首页的，把 logo 的背景图片给链接即可。**

**3. 为了搜索引擎的收录，链接里要放文字（网站名称），但是文字不能显示出来。**

- 方法一：<span style="color: red">text-indent </span>移到盒子外面（<span style="color: red">text-indent: -9999px; </span>），然后<span style="color: red">over-flow: hidden;</span> （淘宝的做法）
- 方法二：直接给<span style="color: red">font-size: 0; </span>就看不到文字了（京东的做法）。

**4. 最后给链接一个<span style="color: red">title</span>属性，这样鼠标放到 logo 上就可以看到提示文字。**

示例：

```
  <header class="header">
    <div class="logo">
      <h1>
        <a href="index.html" title="这是一个网站名称">这是一个网站名</a>
      </h1>
    </div>
  </header>
```

css 部分省略。
