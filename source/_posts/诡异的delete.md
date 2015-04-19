title: "诡异的delete"
date: 2010-09-01 17:35:07
categories: 技术
tags: javascript
---

delete这个操作符呢，在javascript中不是很常用，但是他的特性的确很诡异。

1，删除对象的属性，代码：

{% codeblock %}
var o = {
    a: 1,
    b: 2    
};
delete o.a;
alert(o.a);  //undefined
{% endcodeblock %}

<!--more-->

那么到底delete删除的是对象的属性还是对象的属性值呢，我开始觉得删除的应该是值，因为结果是undefined，而没有报错。但是事实上，我的看法是错误的，举例说明：

{% codeblock %}
var o = {};
var a = { 
    pro: "zhenn"
};
o.c = a;
delete o.c;    //删除对象o的属性a
console.log(o.c);     // undefined
console.log(a.pro);   // zhenn
{% endcodeblock %}


通过上述代码，不难看出在delete o.c之后呢，并没有删除o.c所指向的值，也就是对象a依然存在，否则a.pro应该是过不了编译这关的。说到这里，可以这么理解delete删除对象 的属性，其实相当于删除了对对象中属性值的引用而已，但是这个值依然在对象栈中！

2，对数组的操作，先看代码：


{% codeblock %}
var arr = [1,2,3];
delete arr[2];
console.log(arr.length);  // 3
console.log(arr);   // [1,2,undefined]
{% endcodeblock %}

又一次证明了，delete并没有真正删除元素，只是删除了元素所对应的键值。为了更进一步认清delete的本质，和Array中的pop方法比较一下。如下：

{% codeblock %}
var arr = [1,2,3];
arr.pop();
console.log(arr);  // [1,2]
console.log(arr.length)  // 2
{% endcodeblock %}

3，以上对对象和数组的操作，还很好理解，但是对于变量的操作，难免让人琢磨不透，代码如下：

{% codeblock %}
var a = 1;
delete a;
alert(a); // 1
   
function fn(){ return 42; }
delete fn;
alert(fn());  // 42
   
b = 2;
delete b;
alert(b);  // b is not defined;
{% endcodeblock %}

很难解释通啊，同样是全局变量，用var声明的竟然删除不了，而直接声明的变量b竟然可以删除，不能不说delete很诡异了，在ECMA给出的解释中，也仅仅是说通过var声明的变量和通过function声明的函数拥有DontDelete特性，无法被删除。