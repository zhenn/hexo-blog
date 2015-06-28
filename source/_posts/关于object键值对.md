title: "关于object键值对"
date: 2011-05-05 13:39:09
categories: 技术
tags: javascript
---

对于object的取值，一种是通过逻辑定位符（.），另外一种就是通过hash map的方式。对于第一种方法，没有过多的疑问，对象标识符.对象键标识符，如var o = { a: 1} ; o.a就取到对象o的属性a的值，如果用哈希表的方法取，则为o['a']，那么一定要o['a']才可以吗？

是否有其他方法呢？
<!--more-->
看下面这段“怪异”的代 码：

{% codeblock %}
var o = {
    a: 1,
    false : 2,
    1: 3
};
o[{aa:1}] = 4;
o[{bb:1}] = 5;
o['aa:1'] = 6;
console.log(o[[['a']]] == o[['a']] == o['a']);   //true
console.log(o[false] == o.false);   //true
console.log(o[1] == o['1']);      //true
console.log(o[{aa:1}] == o['aa:1']);    //false
console.log(o['[object Object]'])   //5
console.log(o[{aa:1}]);    //5
console.log(o);
{% endcodeblock %}

说它怪异，是因为可能平时很少遇到这样的代码，也和自己的编码习惯有差异，比如当取对象o的a属性值时，我想没有人会通过o[[['a']]]或者o[['a']]去取，这实际上是通过复杂的方式来完成简单的任务，但事实上，通过这样的方法，是可以正常取到属性值的。

通过上面的代码片段，可以很容易的得到一条结论，通过hash map的方式获取对象的属性值，object[key]，如果key的数据类型不是string，那么JavaScript解释器会试图把key转为 string类型，也就是key执行了toString方法，并把key.toString()返回的字符串作为对象的一个键，继续在对象中查找是否有对 该属性的声明，即object[key.toString()]。

由这个理论推理，上述代码运行的结果就很好理解，过程如下：

{% codeblock %}
o[[['a']]] ==> o[[['a']].toString()] ==> o['a']
o[['a']] ==> o[['a'].toString()] ==> o['a']
o[false] ==> o[false.toString()] ==> o['false'] ==> o.false
o[1] ==> o[1.toString()] ==> o['1']
o[{aa:1}] ==> o[{aa:1}.toString()] ==> o['object Object'] == 5
o['aa:1'] == 6
{% endcodeblock %}

而对象o经历了赋值操作o[{aa:1}] = 4;o[{bb:1}] = 5;后，实际上就是o['object Object']的值从4变成了5，所以最后o['object Object']的值是5就很好理解了。

