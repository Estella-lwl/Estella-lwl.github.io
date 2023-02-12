---
layout:
  - post
title: Git推送部分文件
abbrlink: 65f9be6e
author: polariis
top: true
cover: false
toc: true
mathjax: false
tags:
  - Git
categories: Git
date: 2021-05-21 21:08:24
img:
coverImg:
password:
summary:
---



**步骤**：

<span style="color: #0091ff">1.</span> <span style="color: #FFB700">git status</span>                                查看未提交文件及其路径

<span style="color: #0091ff">2.</span> <span style="color: #FFB700">git add 文件路径 </span>                   按需暂存文件，文件路径使用git status的结果

<span style="color: #0091ff">3.</span> <span style="color: #FFB700">git stash -u -k</span>                         忽略未被暂存的文件，以免影响后续commit。

<span style="color: #0091ff">4.</span> <span style="color: #FFB700">git commit -m "update"</span>          后续正常推送流程

<span style="color: #0091ff">5.</span> <span style="color: #FFB700">git push</span>

<span style="color: #0091ff">6.</span> <span style="color: #FFB700">git stash pop</span>				恢复被忽略文件。
