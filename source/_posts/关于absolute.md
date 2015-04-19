title: "关于absolute"
date: 2008-04-12 15:05:42
categories: 技术
tags: [css]
---

记得刚认识absolute的时候，不知道在哪本参考书上看到过关于关于它的介绍，大概意思是这样的，“一个定义了absolute的子级元素（以下简称child）总离不开一个定义relative的父级元素（以下简称parent），否则child将相对于body定位”，时至今日，我不敢说这样的理解是错误的，但是难免有点歧义或者以偏概全，详情以下分解。


### 实例一：

![img](/img/1.jpg)

<!--more-->

代码如下：

{% codeblock %}
<div id="container_091028" style="width:300px; height:200px; margin:16px auto; background:gray;">
　　<div style="width:100px; height:100px; background:green;"></div>
　　<div style="width:100px; height:100px; position:absolute; margin:-10px; background:purple;"></div>
</div>
{% endcodeblock %}

<h3>实例二：</h3>

如果将绿色块对应的html代码（未设置absolute）放在紫色块对应的html代码的下面，按照html由上而下的解析顺序，也就是先解析absolute，后解析普通流元素，那么显示效果将如下

![img](/img/2.jpg)

上面的这两种情况中，仅仅用margin来控制child的位置，并没有用left、top。原因很简单，在parent中并未设置relative，而child设置了absolute，如果在此时用left、top来控制child的位置，后果可想而知，会出现child脱离parent元素而去亲近body的现象，这也恰恰印证了参考书中那句话的正确性。之所以说它有歧义，就在于它缺少了一个很重要的前提条件（child设置left、top）！

对于给每个absolute的子级元素都嵌套一个relative的父级元素的情况，我认为是absolute最标准的用法，相信也是最容易理解的，其优点在这里不再赘述！另外友情提示一下，在css2中文手册（苏沈小雨版）中关于position定位的说明有失精准，见下图：

![img](/img/3.jpg)

其中“此时对象不具有边距”是不准确的，事实上不但有边距，而且其边距还可以和eft、top值叠加！
