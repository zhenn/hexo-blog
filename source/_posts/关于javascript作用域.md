title: "关于javascript作用域"
date: 2010-07-12 16:47:02
categories: 技术
tags: javascript
---

今天一个挚友给我出了道javascript笔试题，代码如下：

{% codeblock %}
function test(){
    var a = b = 2;
}
test();
alert(b);
alert(a);
{% endcodeblock %}

<!--more-->

我看到这段代码，我笑了，很自信的回答道，会报错，因为a,b都是局部变量，所以a and b is not defined。然后我朋友笑了，迷惑ing，难道我错了吗？迟疑了10几秒，原来a是局部变量，而b没有在test中声明，故而是全局变量，很显然，在 这种情况下，执行情况应该是b=2,a is not defined；后来想想，自己还真是缺乏点思维的严谨性啊，特发此贴，以儆效尤！
