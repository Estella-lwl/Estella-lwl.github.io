---
layout:
  - post
title: JS获取视频任意进度
abbrlink: 5ced3654
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - JavaScript
categories: JavaScript
date: 2022-09-16 00:00:00
img:
coverImg:
password:
summary:
---



- 利用视频的<span style="color: #0091ff;background-color: #f2f4f5;"> duration </span>获取视频相关信息。
- 利用<span style="color: #0091ff;background-color: #f2f4f5;"> addEventListener </span>事件监听的相关事件👇🏻，可以获取各个播放进度&在不同阶段操作数据。
  - <span style="color: #678f00">play</span>： 视频开始播放 。
  - <span style="color: #678f00">playing</span>： 视频播放过程中 。
  - <span style="color: #678f00">ended</span>： 视频结束播放 。
  - <span style="color: #678f00">loadedmetadata </span>事件： 音频/视频的元数据已加载时（时长、尺寸（仅视频）以及文本轨道）。
- 当 音频/视频 处于加载过程中时，会 依次发生 以下事件：

- 1. <span style="color: #3850b8">loadstart</span>
  2. <span style="color: #3850b8">durationchange</span>
  3. <span style="color: #3850b8">loadedmetadata</span>
  4. <span style="color: #3850b8">loadeddata</span>
  5. <span style="color: #3850b8">progress</span>
  6. <span style="color: #3850b8">canplay</span>
  7. <span style="color: #3850b8">canplaythrough</span>



<span style="color: #CC0000">1.</span> 在视频第6秒时显示一个按钮：

```
    function videoInit() {
      let elevideo = document.getElementById('video2');
      elevideo.addEventListener('loadedmetadata', () => {
        //视频的总长度
        console.log('总长度:', elevideo.duration);
        clearInterval(this.timer);
      });
      
      elevideo.addEventListener('play', () => {
        //播放开始执行的函数
        console.log('开始播放');
      });
      
      elevideo.addEventListener('playing', () => {
        //播放中
        console.log('播放中');
        this.timer = setInterval(() => {
          console.log('播放进度:' + parseFloat(elevideo.currentTime));
          if (parseInt(elevideo.currentTime) === 6) {
            const btn = document.querySelector('.main-button')
            btn.classList.remove('main-button-hide')
          }
        }, 100);
      });
      
      elevideo.addEventListener('ended', () => {
        //结束
        console.log('播放结束');
        clearInterval(this.timer);
      },false);
      
    };
    videoInit();
```



<span style="color: #CC0000">2.</span> 改进：

```
      eleVideo.addEventListener('playing', () => {
        //播放中
        console.log('播放中');
        const timer = setInterval(() => {
          console.log('播放进度:' + parseFloat(eleVideo.currentTime));
          if (Math.round(eleVideo.currentTime) === 6) {
            const btn = document.querySelector('.main-button')
            btn.classList.remove('main-button-hide')
          }

          if (eleVideo.currentTime > 6) {
            console.log('结束监听');
            clearInterval(timer);
          }
        }, 1000);
      });
```



<span style="color: #CC0000">3.</span> 改进使用video的 currentTime 事件后：

```
    function videoInit() {
      let eleVideo = document.getElementById('videoEl');
      eleVideo.addEventListener('timeupdate', () => {
        console.log('播放中');
        if (Math.round(eleVideo.currentTime) === 6) {
          console.log('第6秒了');
          const btn = document.querySelector('.main-button')
          btn.classList.remove('main-button-hide')
        }
      })
    };
    videoInit();
```

