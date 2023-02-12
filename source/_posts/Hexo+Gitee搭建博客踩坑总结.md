---
title: Hexo+Gitee搭建博客踩坑总结
abbrlink: hexo-gitee
date: 2021-04-04 06:37:56
author: polariis
img: 
top: true
cover: true
coverImg:  
password:  
toc: true
mathjax: false
summary:  
categories: hexo
tags: 
  - hexo
  - gitee
---

## 一、搭建过程如下：

### 阶段1： 本地搭建

1.下载安装node.js 

2.下载安装git 

3.使用命令行(win+r,输入cmd)下载淘宝镜像：	

```
npm install -g cnpm --registry=https://.npm.taobao.org
```

4.使用命令行下载hexo :	（git bash和cmd都可以）

```
npm install -g hexo-cli
```

5.建立空文件夹 ：

```
md blog
```

6.进入建好的文件夹：

```
cd blog 
```

7.启动服务：

```
hexo s
```

8.停一下：ctrl c再按y 



9.新建文章： hexo n "标题" 



10.看这时在哪个目录下，然后cd进入 



11.确认新建的文章是否成功：dir 



12.给博文加内容：这里我用typora写。用vim的方法是：vim 标题.md。然后保存退出：先按esc，再输入	wq(w保存，q退出)。 



13.文件退两层： cd../.. (到了blog目录) 



14.win下写博文从文件夹用typora打开文章，然后保存刷新页面，不用重新启动hexo。 



15.清理 :

```
hexo clean 
```

16.生成:

```
hexo g 
```

### 阶段2：部署到Gitee

1.装一个git的部署插件cnpm install --save hexo-deployer-git(win用npm install) 



2.注册登录Gitee账号，新建仓库，设置仓库名（一定要和用户名一样，否则可能会有无法预料结果）。



3.设置根目录的_config.yml文件，用typore打开，拉到最底下设置deploy的值：

```
deploy:
  type: git
  repo: 你的仓库地址（去Gitee复制）
  branch: master
```

注意冒号和值之间一定要有空格，type后面有单引号的话可以直接删了。



4.这样就随时可以用电脑/手机访问了，地址可以直接复制仓库名访问。接下来就是换主题和自定义主题为满意的样式，我用的是Matery，网上有很多，Hexo官网也有。

## 二、需要注意的地方：

1.主题名别忘了在_config.ym中更改，这种配置文件一定要严格按照格式！否则一言不合就报错！



2.下载的主题在解压缩后不要更改



3.注意有没有漏下的插件，检查所有插件及版本号的指令：

```
npm list
```

4.卸载某个插件的指令：

```
npm uninstall 插件名@版本号
```

5.怕出错想找回改动前的文件：可以提前复制粘贴一份在桌面，方便随时对照/改动。

6.Typora可以选择是否显示目录，设置toc: true，如果某篇文章不显示目录了，可以查看文章最上方的设置是否正确。

## 三、踩坑&解决：

1.报错：FATAL err:[Error: EBUSY: resource busy or locked, rmdir 'C:\User\...]

网上搜索了一下，关闭软件重启电脑，果然有奇效。



2.我的页面刚开始一直404，我百思不得其解，大佬们的各路招数试了个遍，然而它的纹丝不动仿佛在告诉我不要浪费时间。

最后！我在Gitee Page页面看到了一个更新的按钮，我一脸疑惑的点了一下。接着，大不一样，顿时体会到柳暗花明又一村天无绝人之路。没错，我想了一下，大概是因为我的git一键部署功能出错了。总之，现在是记了，	每次终端部署完之后，在这个页面再点更新就好了。



3.我发现我的主题，在设置成功之后无法在网站中更新，在尝试了很多方法之后，我发现把根目录的config yml文件的repo由SSH的地址变换为HTTPS的位置，这样就可以成功解决，不过还有一个问题就是这样的话，每次重新部署都要输入用户名和密码。

然后我就尝试在网上搜集一些办法，但是没有什么用。这时我就想，如果我把repo. 再换成SSH的地址。这样换了之后我看会有什么变化变化，结果发现。重新部署之后是可以的，页面刷新也没有完全问题。

4.我在所选的主题下找到了read me，开始按照里面的提示来配置我的主题文件夹。其中有一个是配置评论的表情的。我在配置过后发现你发现在本地运行的时候都会报错，后来在网上查找，提示说检查Jason文件格式，一般情况下，他都是一对花括号是首尾。后来我把最上面那行Json删了也把最外层的中括号删了。结果发现依旧报	错，接着我又尝试把中括号加上，运行一下再也没有红色字段，就Json这四个字母导致了运行报错。

​	5.修改根目录配置文件时，注意缩进，有报错的情况！

​	6.卸载了highlight.js@10.7.1：npm uninstall hexo-qiniu-sync@1.4.7 （相关的配置我暂时未删，试了一下已	经好了）

​	7.设置文章永久链接，下载指令：

```
npm install hexo-abbrlink --save
```

​	设置修改`_config.yml`文件中的配置项（记得把原来的`permalink:`删除掉）:

```
#设置永久链接
permalink: posts/:abbrlink.html  # 此处可以自己设置，也可以直接使用 :/abbrlink
abbrlink:
    alg: crc16   #算法： crc16(default) and crc32
    rep: dec     #进制： dec(default) and hex
```

​	8.npm resource busy or locked报错解决：

```
npm cache verify
```

