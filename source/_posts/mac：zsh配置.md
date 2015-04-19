title: "mac：zsh配置"
date: 2013-04-27 14:27:23
categories: 技术
tags: shell
---

`Zsh`是一款功能强大的交互式 shell，与 Bash 相比，Zsh 下面几点表现令人印象深刻：

- 自动补全
- 拼写纠错
- 定制性强
- 美观的命令提示符（这点吸引力最大）Setup

## 一，安装zsh

`brew install zsh 或 port install zsh`


## 二，设置zsh为默认shell
    
首先查看zsh路径，运行which zsh，通常显示为 /bin/zsh，然后执行`vim /etc/shells`
在文末增加：/bin/zsh

将zsh设置为默认的`Shell：chsh -s /bin/zsh`

## 三，插件管理

oh-my-zsh是基于zsh的功能做了一 个扩展，方便的插件管理、主题自定义，以及漂亮的自动完成效果。

安装过程全自动：
{% codeblock %}
via `curl`
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
{% endcodeblock%}
