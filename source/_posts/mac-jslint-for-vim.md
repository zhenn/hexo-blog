title: "mac:jslint_for_vim"
date: 2012-01-30 14:09:41
categories: 技术
tags: ['jslint','vim']
---

### 1，准备js引擎
 
mac下默认没有裸的js引擎，需手动安装
 

`sudo brew install spidermonkey`
 
安装jslint上的文档说明，安装的软件包应该是spidermonkey-bin，但笔者一直搜不到相关软件包，无奈之下就用了spidermonkey。

### 2，安装ruby
 
jslint程序本身是用ruby写的，所以ruby环境也是必须的
 
`sudo port install ruby`

<!--more-->

### 3，下载jslint插件
 
官网地址：[http://www.vim.org/scripts/script.php?script_id=2729](http://www.vim.org/scripts/script.php?script_id=2729)
 
下载完成后，解压缩文件，进入jslint.vim文件夹，拷贝javascript目录至~/.vim/plugin 

### 4，创建jslint可执行程序
 
将下载文件夹中bin/jslint移动至/usr/bin或~/bin中，如果放在~/bin，需保证此目录已经添加到环境变量

至此，jslint已经配置完毕。在mac（lion）下，使用vim打开js文件，将看到如下提示：
 

`Error detected while processing function 87_JSLint: Line 33: could not invoke JSLint!`
 
这个问题的出现，让笔者困惑了许久，排查了很多不确定因素，最终定位问题出现在js引擎上。解决办法是，使用node作为jslint工作所需的js引擎，打开vim配置文件~/.vimrc，添加如下代码：

{% codeblock %}
" Use Node.js for JavaScript interpretation 
let $JS_CMD='node'
{% endcodeblock %}


ok，jslint可以正常工作啦，在出现js预发错误的行，将出现高亮提示。

![](/img/777.jpg)

