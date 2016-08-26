title: "git配置多个ssh-key"
date: 2016-08-26 14:26:01
categories: 技术
tags: ['git','gitlab','ssh']
---

git是现在最为流行的代码管理方式，除了个人使用的github，在公司内通常也会架设gitlab服务做项目代码管理。那么，就需要正确地管理本地ssh公钥，来正常使用不同的git服务。

<!--more-->

## 生成多个ssh-key

### github

	$ ssh-keygen -t rsa -C "youremail@your.com” -f ~/.ssh/id_rsa_github

在~/.ssh/目录会生成github-rsa（私钥）及github-rsa.pub（公钥），将公钥内容配置自己github账户中即可。

### gitlab

	$ ssh-keygen -t rsa -C "youremail@yourcompany.com” -f ~/.ssh/id_rsa

同上，将生成gitlab所需公钥、公钥，将公钥配置到gitlab账户。

## 修改配置文件

在~/.ssh中新建config文件

	$ touch config

添加内容

	# gitlab
	Host gitlab.com
	    HostName gitlab.com
	    PreferredAuthentications publickey
	    IdentityFile ~/.ssh/id_rsa
	# github
	Host github.com
	    HostName github.com
	    PreferredAuthentications publickey
	    IdentityFile ~/.ssh/id_rsa_github

## 测试

	$ ssh -T git@github.com

输出'Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.'表示已成功连接github，同理测试公司gitlab。


