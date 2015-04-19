title: "node及node模块升级"
date: 2014-08-10 14:47:24
categories: 技术
tags: ['node','npm']
---

node版本升级原始方法，通过下载源码在本地重新编译，过程相对繁琐。实际上，node中有一个版本管理模块n，可一键升级node版本。

### 第一步，先安全n模块

`npm install -g n`

### 第二步，一键升级

升级到稳定版：`n stable`
升级至指定版：`n 0.10`

<!--more--> 

so easy！

众所周知，node模块的升级方式

`npm update module`

其他npm常用命令：

- npm -v                   #显示版本，检查npm 是否正确安装
- npm install express      #安装express模块
- npm install -g express   #全局安装express模块
- npm list                 #列出已安装模块
- npm show express         #显示模块详情
- npm update               #升级当前目录下的项目的所有模块
- npm update express       #升级当前目录下的项目的指定模块
- npm update -g express    #升级全局安装的express模块
- npm uninstall express    #删除指定的模块
