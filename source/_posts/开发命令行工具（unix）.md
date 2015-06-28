title: "开发命令行工具（unix）"
date: 2011-12-10 13:57:42
categories: 技术
tags: ['命令行','node']
---

本文拿node程序举例
 
一，创建node程序nodetool，代码如下：

{% codeblock %}
#!/usr/bin/env node
console.log(process.argv);
{% endcodeblock %}

第一行声明执行文件的程序名称，从env中自动检索node，也可以设置node程序的具体位置，如：/usr/local/bin/node，正文部分即js程序。
 
查找node程序所在目录，使用whereas(linux)、which（mac）可快速定位，如which node

<!--more-->

二，使用chmod命令将nodetool改为可执行文件，命令：chmod +x nodetool，此时在当前目录（nodetool所在目录）下，输入命令nodetoll target1 target2，在终端的输出界面将看到数组[ 'node', '/Users/donghan/bin/nodetool', 'target1', 'target2' ]，前两个元素分别是使用的程序文件node和/Users/donghan/bin/nodetool，target1和target2是执行nodetool时的两个额外参数。

想更加方便的处理额外参数，推荐使用commander，一个基于node的第三方模块，项目地址：[http://nodetoolbox.com/packages/commander](http://nodetoolbox.com/packages/commander)。

三，修改环境变量，使用全局命令行
 
经过以上两个步骤，nodetool已经完全可以使用，但局限在于只能在当前目录使用，如果想在任意目录使用nodetool程序（即全局命令行），需要修改环境变量。
 
打开用户主目录（根）下.bash_profile文件，如果不存在则创建一个新的文件，添加如下字段：

`export PATH=$PATH:/Users/donghan/bin;`

即导出PATH变量，其中/Users/donghan/bin是nodetool所在目录，此处根据可执行文件所在目录不同自行配置。需要注意的是，导出之前先获取$PATH用英文冒号(:)连接新的path，避免覆盖之前的path。
 
编辑文件保存之后，如想立即生效，在用户主目录下执行以下命令：

`. .bash_profile`

另外一种方法可通过重启机器生效，当然不如执行单条命令快捷。
 
至此，nodetool命令行开发完毕。
