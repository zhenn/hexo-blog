title: "mac配置vimzencoding"
date: 2012-01-19 14:03:23
categories: 技术
tags: ['zencoding','vim']
---

无论你使用什么样的文本编辑器，zencoding作为一种很酷的编码方式，倍受开发人员的青睐。本文简单介绍mac vim下的zen coding配置：
 
一，下载zen coding插件
官方github地址：[https://github.com/mattn/zencoding-vim](https://github.com/mattn/zencoding-vim)
 
可使用git下载，也可以下载zip包。
文档说明中有些地方不是很详尽，下面稍加补充说明。
 
二，拷贝文件到vim目录
 
首先解压缩zip包`zencoding-vim-master.zip`，进入zencoding-vim-master文件夹，拷贝该目录下的所有文件到剪贴板。进入用户根目录`（~/username）`，找到.vim文件夹（如不存在则自行创建），粘贴剪贴板中的内容到.vim根目录。

<!--more-->
 
三，使用zen coding开始编码之旅

经过以上两步，使用vim编辑.html文件，在插入模式下，输入html:5_（_代表光标位置），按下control+y+,（逗号不可省略） ，在编辑中可生成如下代码：

{% codeblock %}
<!DOCTYPE HTML> 
<html lang="en"> 
<head> 
<title></title> 
<meta charset="UTF-8"> 
</head> 
<body> 
    
</body> 
</html>
{% endcodeblock%}

四，简单的配置
 
以上生成的结果或许并不符合自己开发的要求，但没有关系，zencoding提供开发者配置文件修改。
 
配置文件所在目录：`.vim/autoload/zencoding.vim` 
 
打开此配置文件，搜索'html:5'找到相关的snippet，修改成自己想要的结果即可。例如笔者目前从事web无线配置，配置如下：

{% codeblock %}
\            'html:5': "<!doctype html>\n"
\                    ."<html manifest=\"appcache.manifest\">\n"
\                    ."<head>\n"
\                    ."<meta charset=\"utf-8\">\n"
\                    ."<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0\" />\n"
\                    ."<meta name=\"format-detection\" content=\"telephone=no\" />\n"
\                    ."<meta name=\"apple-mobile-web-app-capable\" content=\"yes\" />"
\                    ."<title></title>\n"
\                    ."<link href=\"\" type=\"text/css\" rel=\"stylesheet\" />\n"
\                    ."<script src=\"\" type=\"text/javascript\"></script>\n"
\                    ."<link rel=\"apple-touch-icon\" sizes=\"57x57\" href=\"\" />\n"
\                    ."</head>\n\n"
\                    ."<body>\n\t${child}|\n</body>\n"
\                    ."</html>"
{% endcodeblock%}

再次使用vim编辑html文件，输入html:5_，展开代码将看到如下结果：

{% codeblock %}
<!doctype html>
<html manifest="appcache.manifest">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" />
<meta name="format-detection" content="telephone=no" />
<meta name="apple-mobile-web-app-capable" content="yes" /><title></title>
<link href="" type="text/css" rel="stylesheet" />
<script src="" type="text/javascript"></script>
<link rel="apple-touch-icon" sizes="57x57" href="" />
</head>
    
<body>
       
</body>
</html>
{% endcodeblock%}

同样的，配置文件包括很多css、js中的书写方法，如果之前并没有接触过zen coding，可详细阅读文件内容进而熟悉zen coding 的编码方式。
 
对于zen coding提供的展开代码快捷键（control + y + ,），笔者本身感觉操作不是很方便，当然这也是可以配置的。只需要编辑vim的配置文件（~/.vimrc），将如下代码写入即可。

{% codeblock %}
"设置zencoding插件展开代码快捷键为ctrl+e
let g:user_zen_expandabbr_key = '<c-e>'
let g:use_zen_complete_tag = 1
{% endcodeblock%}

保存后文件，将覆盖zen coding插件快捷键配置。
