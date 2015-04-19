title: "如何开发并发布node插件到npm平台"
date: 2013-10-19 14:34:57
categories: 技术
tags: ['npm']
---

无论是为了自己编写的node程序可以方便第三方使用（足够通用），还是仅仅出于对团队协作考虑（仅团队中使用），使用npm平台都是不错的选择，操作也很简介。

## 一，在npm平台创建账号

理所当然，当上传压缩包到一个公用平台，没有身份标识显然行不通。因此，第一步需要到npm官方网站注册账号，地址：[https://www.npmjs.org/](https://www.npmjs.org/)，记住你的name 和 pwd。

## 二，编写node程序

这里特殊说明，如果你想开发grunt插件，请使用grunt官方提供的创建插件方法初始化项目，具体请参考：[http://www.gruntjs.org/docs/creating-plugins.html](http://www.gruntjs.org/docs/creating-plugins.html).

通过这种方法，在package.json配置文件中会自动添加keyword：gruntplugin ，有了这个配置项即可保证你开发的npm模块被收集到grunt插件平台http://gruntjs.com/plugins

若只是普通的node库，则忽略此步骤。

<!--more-->

## 三，上传操作

进入项目目录，首先通过npm adduser命令添加创建者相关信息，依次录入name、pwd及email。然后，执行npm publish命令即可将项目上传至npm。

若你所知，别人通过`npm install <你的项目成名> `即可使用你所贡献的node程序包。

——补录——
npm其他常用api：
npm link  把当前目录代码当做全局模块使用，开发npm模块时必用
npm init  初始化一个npm模块，其实仅仅创建了package.json
npm update 更新node模块

多次发布，需要保证package中版本号递增，否则会发布失败.

——补录（2015-03-21）—— 

# 如何让开发的node模块通过全局shell命令调用？

基本钩子即可实现，如开发monkey模块，在package.json中约定bin配置如下：

`"bin" : {
        "monkey" : "./bin/monkey"
  }`

如果monkey模块只有一个可执行命令，即monkey，可简写为

`"bin" : "./bin/monkey"`

其中，./bin/monkey为可执行文件，并声明其执行环境，比如

`#!/usr/local/bin/node --harmony`

标示该文件通过node的可兼容模式运行（node中使用ES6的标准，需要--harmony），否则会有语法报错！
通过以上配置，在本地开发模块时，通过npm link即可直接通过monkey命令调起./bin/monkey。同样的，在发布到npm官方后，他人通过npm install monkey亦可直接使用monkey命令了！

## package.json中其他约定

- name、version
	- 必须字段，否则无法通过npm install安装
- keywords
	- 关键字
	- 字符串组成的数字，便于在npm上检索到
- description
	- 项目描述
	- 同样便于在npm上检索
- homepage
	- 官网url
- bugs
	- 提交问题的网址、email，便于使用者反馈问题
	- 格式：json { "url" : "xxx" , "email" : "xxxx" }
- license
	- 许可证，让别人知道使用的权利和限制
- author
	- 作者
- contributors
	- 贡献者
	- 数组
- main
	- 标示项目的入口
	- 使用者通过require('xx')，可访问入口文件下所有api
- man
	- 指定文档文件，调用man xx时调用的文件
	- 路径 | 路径数组
- repository
	- 指定项目的仓库类型及地址
	- 如：{  "type" : "git",  "url" : "http://github.com/isaacs/npm.git" }
- dependencies
	- 项目依赖,json
	- 当npm link或install时，将自动下载安装
