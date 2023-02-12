---
layout:
  - post
title: Git代码写错分支了怎么办
abbrlink: e1ab1244
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - Git
categories: Git
date: 2021-12-14 18:39:41
img:
coverImg:
password:
summary:
---



2021/12/14 11:07

**场景**：代码写在了主分支，在提交时显示”推送被拒“。

**步骤：** 

### **1.当未提交代码**：

```
// 第一步，将修改的代码暂存到stash
git stash

// 第二步，切换到正确的分支
git checkout targetbranch 

// 第三步，从stash中取出暂存的代码修改。
git stash pop
```

​             

继续提交的话就是

```
git add .git commit -m xx  
```



### **2. 当已经提交了：**

撤回即可

```
git reset HEAD^              
```

然后继续执行 没提交时的操作。



### **3.** **已经push了：** 

同已经提交了的步骤。 先撤回，然后再push下撤回后的。不过会有记录

**PS： 期间还有别的报错：** 

- 本地与远程冲突：git pull
- error: The following untracked working tree files would be overwritten by ch： 大概也是因为未拉取。
- Branch 'dev-test2' set up to track remote branch 'dev-test2' from 'origin'.：
  - 1.可以使用：git status 查看什么情况，
  - 2.原因可能是这些变化没有提交，根据git status 的提醒，可以提交，然后就可以切换分支了。
  - 3.git checkout 要切换的分支。
