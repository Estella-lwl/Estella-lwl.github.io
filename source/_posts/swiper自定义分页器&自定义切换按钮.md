---
layout:
  - post
title: swiper自定义分页器&自定义切换按钮
abbrlink: 7b1d00b1
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - 插件&工具使用
  - swiper
categories: 插件&工具使用
date: 2021-11-12 00:00:00
img:
coverImg:
password:
summary:
---



### 1. swiper自定义分页器：

js部分：

```
pagination: {
  el: '.swiper-pagination',
  type: 'custom',
  renderCustom: function (swiper, current, total) {
    // 分页器激活样式的改变---给自己添加激活样式并将兄弟的激活样式移出。
    // $('.swiper-pagination').children().eq(current - 1).addClass('active').siblings().removeClass('active');
    var paginationHtml = " ";
    for (var i = 0; i < total; i++) {
      // 判断是不是焦点，是的话添加active类，不是就只添加基本样式类
      if (i === (current - 1)) {
        paginationHtml += '<span class="swiper-pagination-customs swiper-pagination-customs-active"></span>';
      } else {
        paginationHtml += '<span class="swiper-pagination-customs"></span>';
      }
    }
    return paginationHtml;
  }
},
```

​           

上面的代码可以忽略：

下面是最终的：

```
renderCustom: function (swiper, current, total) {
  // 分页器激活样式的改变---给自己添加激活样式并将兄弟的激活样式移出。
  $('.swiper-pagination').children().eq(current - 1).addClass('active').siblings().removeClass('active');
  var paginationHtml = " ";
  for (var i = 0; i < total; i++) {
    if (i === (current - 1)) {
      // paginationHtml += '<li class="swiper-pagination-customs swiper-pagination-customs-active"></li>';
      if (i === 0) {
        paginationHtml += '<li class="swiper-pagination-customs swiper-pagination-customs-active"><img src="./static/imgs/gamedetail/icon1.png" alt=""><p>猎人来了我怕怕</p></li>';
      } else if (i === 1) {
        paginationHtml += '<li class="swiper-pagination-customs swiper-pagination-customs-active"><img src="./static/imgs/gamedetail/icon2.png" alt=""><p>幼犬皮皮</p></li>';
      } else if (i === 2) {
        paginationHtml += '<li class="swiper-pagination-customs swiper-pagination-customs-active"><img src="./static/imgs/gamedetail/icon3.png" alt=""><p>正义街道儿</p></li>';
      } else if (i === 3) {
        paginationHtml += '<li class="swiper-pagination-customs swiper-pagination-customs-active"><img src="./static/imgs/gamedetail/icon4.png" alt=""><p>3A大作</p></li>';
      }
    } else {
      // paginationHtml += '<li class="swiper-pagination-customs"></li>';
      if (i === 0) {
        paginationHtml += '<li class="swiper-pagination-customs"><img src="./static/imgs/gamedetail/icon1.png" alt=""><p>猎人来了我怕怕</p></li>';
      } else if (i === 1) {
        paginationHtml += '<li class="swiper-pagination-customs"><img src="./static/imgs/gamedetail/icon2.png" alt=""><p>幼犬皮皮</p></li>';
      } else if (i === 2) {
        paginationHtml += '<li class="swiper-pagination-customs"><img src="./static/imgs/gamedetail/icon3.png" alt=""><p>正义街道儿</p></li>';
      } else if (i === 3) {
        paginationHtml += '<li class="swiper-pagination-customs"><img src="./static/imgs/gamedetail/icon4.png" alt=""><p>3A大作</p></li>';
      }
    }
  }
  return paginationHtml;
}
```

​       

2021/12/23 16:26 更新

### 2. 自定义切换按钮：

效果：

![image-png](https://s2.loli.net/2022/10/05/QoZKAk5LDpW1txN.png)

html代码：

```
<div class="swiper-button-next drag-right"></div>
```

配置项：           

```
navigation: {
  nextEl: '.swiper-button-next',
},
```

css代码：

```
/*去掉默认样式*/
.swiper-button-next:after {
  display: none;
}

.swiper-button-next {
  background: url("https://vplus-1302389019.cos.ap-guangzhou.myqcloud.com/mobile/slideRight.png")
    no-repeat;
  background-size: 100% 100%;
  bottom: 6rem;
  width: 1rem !important;
  height: 1.3rem !important;
}

.drag-right {
  bottom: 6rem;
  right: 0.2rem;
  position: absolute;
  z-index: 888;
  width: 1rem;
  height: 1.3rem;
  animation: 1.2s ease-in-out 0s infinite normal none running btn-run;
  -webkit-animation: btn-run 1.2s infinite;
  -moz-animation: btn-run 1.2s infinite;
  -ms-animation: btn-run 1.2s infinite;
}
@keyframes btn-run {
  0% {
    right: 0.2rem;
    transform: scale(1);
  }
  15% {
    right: 0.28rem;
    transform: scale(1);
  }
  30% {
    right: 0.36rem;
    transform: scale(1.05);
  }
  50% {
    right: 0.43rem;
    transform: scale(1.1);
  }
  70% {
    right: 0.36rem;
    transform: scale(1.05);
  }
  85% {
    right: 0.28rem;
    transform: scale(1);
  }
  100% {
    right: 0.2rem;
    transform: scale(1);
  }
}

.drag-right img {
  width: 100%;
  height: 100%;
}
```

**PS:** 

自定义按钮去掉点击时的蓝色边框：

```
cursor: none;
// 或删掉：
cursor: pointer;
```

