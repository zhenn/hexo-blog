title: "javascript设计模式学习笔记之“类式继承”"
date: 2010-07-02 13:41:59
categories: 技术
tags: javascript
---

在做一件事情之前，首先要清楚做这件事情的好处有什么，相信不会有哪个人愿意无缘无故的去做事情。一般说来，我们在设计类的时候，实际上就是希望能减少重复性的代码，使用继承可以完美的做到这一点，借助继承机制，你可以在现有类的基础上再次进行设计并且充分利用它们已经具备的各种方法，而对设计的修改也更为轻松。

废话不多说了，举例说明：

<!--more-->

{% codeblock %}
function Person(name){
    this.name = name;
}
Person.prototype.getname = function(){
    return this.name;
}
   
function Bloger(name,blog){
    Person.call(this,name);
    this.blog = blog;
}
var bloger = new Bloger("zhenn","http://www.men-ideal.com");
alert(bloger.name=="zhenn");   /*返回ture*/
alert(bloger.blog)   /*提示http://www.men-ideal.com*/
alert(bloger.getname()=="zhenn");   /*提示"bloger.getname is not a function"*/
{% endcodeblock %}

通过上例可以看到，Bloger在其内部通过call动态调用了其父类Person的原生属性和方法（关于call的讲解请参考），即可以理解为Bloger继承了Person，成为它的一个子类，但是细心的同学会发现，Person原型对象中的方法，仅仅依靠call，是不能继承过来的，这也就是会提示"bloger.getname is not a function"的原因所在了。不过不用担心，对上述代码稍作处理，即可解决这个问题！

{% codeblock %}
function Person(name){
    this.name = name;
}
Person.prototype.getname = function(){
    return this.name;
}
   
function Bloger(name,blog){
    Person.call(this,name);
    this.blog = blog;
}
/*请注意以下两行代码*/
Bloger.prototype = new Person();   
Bloger.prototype.constructor = Bloger;
   
var bloger = new Bloger("zhenn","http://www.men-ideal.com");
alert(bloger.name=="zhenn");   /*返回ture*/
alert(bloger.blog)   /*提示http://www.men-ideal.com*/
alert(bloger.getname()=="zhenn");   /*提示true*/
{% endcodeblock %}

在这里需要对这两行代码解释一下，我们知道，每一个构造函数都有一个prototype属性，这个属性指向该构造函数的原型对象，其实原型对象也是实例对象，只不过在原型对象中定义的属性和方法可以提供给所有的实例对象共享，由此可以得出，新添加两行代码的意图就是设置子类的原型对象指向父类的一个实例化对象，而父类的实例化对象会把父类的原型属性方法统统继承过来，这样也就达到了我们的目的，子类的原型继承了所有父类实例对象具有的属性和方法。

但是还应该注意Bloger.prototype.constructor = Bloger;这行代码，因为把子类的prototype设置为父类的实例时，其constructor属性会指向父类，所以要设置子类原型的constructor重新指向子类，至此，已经完美实现了javascript的类式继承！

为了简化子类的声明，可以把扩展子类的整个过程写在一个名为extend的函数中，作用就是基于一个给定的类结构去创建一个新的类：

{% codeblock %}
function extend(childClass,parentClass){
    var F = new Function();
    F.prototype = parentClass.prototype;
    childClass.prototype = new F();
    childClass.prototype.constructor = childClass;
}
{% endcodeblock %}

有了这个extend这个函数，就可以很方便的扩展子类了，只需调用这个函数即可，上述添加的两行代码可改为extend(Bloger,Person), 一样可以实现完全继承！


