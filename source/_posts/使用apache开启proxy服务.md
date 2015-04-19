title: "使用apache开启proxy服务"
date: 2013-04-30 14:30:49
categories: 技术
tags: ['apache','代理']
---

## 1， 挂载apache相关 proxy模块
打开httpd.conf，把以下代码前的注释符去掉

{% codeblock %}
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_http_module modules/mod_proxy_http.so
{% endcodeblock %}

## 2，启动proxy服务
{% codeblock %}
ProxyRequests on
<Proxy *>
       Order deny,allow
       Allow from all
       #Deny from 127.0.0.1
</Proxy>
{% endcodeblock %}

## 3，设完手工，移动终端即可不用绑定host访问开发机上的测试页面
