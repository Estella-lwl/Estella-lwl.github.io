---
layout:
  - post
title: 新建分支后与本地git建立联系
abbrlink: c35f114b
author: polariis
top: true
cover: false
toc: false
mathjax: false
tags:
  - Git
categories: Git
date: 2021-09-28 22:54:25
img:
coverImg:
password:
summary:
---



1. 先查看所有分支：

```
git branch -a    
```

​          

2. 获取所有分支，这时就可以看到新的分支出现了。

```
git fetch     
```

​         

3. 新建&切换分支：

```
 git checkout -b XXX              
```



4. 和远程分支建立联系：

```
git branch -u origin/XXX XXX
```

​              
