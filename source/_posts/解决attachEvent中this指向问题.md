title: "解决attachEvent中this指向问题"
date: 2011-04-03 19:50:51
categories: 技术
tags: ['javascript','dom']
---

给dom元素绑定事件监听，很easy，即使刚入门的javascript编程人员都可以办到，比如obj.onclick = fn就可以很轻松的监听obj的单击事件，这固然是没有问题的，但是问题就在于在这个监听过程中，很难触发N个代码片段，例如：

{% codeblock %}
obj.onclick = fn1;
obj.onclick = fn2;
...
{% endcodeblock %}

<!--more-->

事实上，如果这么做，fn2会把fn1覆盖，也就是说在单击obj时只会执行fn2中的代码片段，fn1则会被忽略，很显然这样不能满足我们的需求。在考 虑到这个问题的时候，JS已经为我们准备了像attachEvnet、addEventListener这样的方法来满足我们的需求，虽然在不同级别的浏 览器中方法不尽相同，利用对browser的能力检测还是可以轻松的实现一个兼容的方法：

{% codeblock %}
function bindEvent(elem,type,fn){
    if(elem.attachEvent){
        elem.attachEvent("on"+type,fn);
    }else{
        elem.addEventListener(type,fn,false);
    }
}
//调用
bindEvent(obj,"click",fn1);
bindEvent(obj,"click",fn2);
bindEvent(obj,"mouseover",function(){alert(this)})   //this->window
{% endcodeblock %}

这样目的就达到了，当单击obj时，依次执行fn1、fn2。解决了这个问题后，细心的淫们会发现在执行的function中this执行是有问题的，始 终指向window，又不是一个不小的麻烦。那么有没有办法，来解决这个棘手的问题呢，答案是肯定的，这正是我写这篇文章的意义所在，废话!

其实只需对bindEvent稍作加工即可，如下：

{% codeblock %}
function bindEvent(elem,type,fn){
    if(elem.attachEvent){
        elem.attachEvent("on"+type,function(){
            fn.apply(elem,arguments);
        });
    }else{
        elem.addEventListener(type,fn,false);
    }
}
bindEvent(obj,"mouseover",function(){alert(this)})  //this->elem
{% endcodeblock %}

理论上，这样的解决方案近乎完美了，但是还有一个问题亟待解决，因为在attachEvent中使用了匿名函数来执行fn，导致在无法使用 detachEvent来删除绑定的某个代码片段，也许obj["on"+type] = null可以解决，清空绑定的代码片段，这很粗暴邪恶，同时也违背我们的业务需求。

我想要从根本上解决这些问题，就一定抛弃attachEvent这个方法，事实上我也是这样做的，如下：

{% codeblock %}
//绑定事件
function bindEvent(elem,type,fn){
    if(elem.attachEvent){
        var typeRef = "_" + type;
        if(!elem[typeRef]){
            elem[typeRef] = [];
        }
        for(var i in elem[typeRef]){
            if(elem[typeRef][i] == fn){
                return;
            }
        }
        elem[typeRef].push(fn);
        elem["on"+type] = function(){
            for(var i in this[typeRef]){
                this[typeRef][i].apply(this,arguments);
            }
        }   
    }else{
        elem.addEventListener(type,fn,false);
    }
}
    
//移除事件绑定
function removeEvent(elem,type,fn){
    if(elem.detachEvent){
        if(elem["_"+type]){
            for(var i in elem["_"+type]){
                if(elem["_"+type][i] == fn){
                    elem["_"+type].splice(i,1);
                    break;
                }
            }
        }
    }else{
        elem.removeEventListener(type,fn,false);
    }
}
{% endcodeblock%}

这段代码有点饶人，首先根据type类型给elem注册一个相关的属性_type，来存放elem同种类型事件要执行的代码片段，当然在这个过程中 要先判断这个属性是否已经存在，如果存在，则跳过这一步。然后要遍历这个存放代码片段的数组，判断是否有和要添加的代码片段相同的代码片段，如果有，直接 跳出function。假设在上一步条件不满足的情况下（即代码片段不重复），那么把这个代码片段push到相应的数组中。之后干的事情，我想大家应该很 清楚了，没错，就是把相应数组里的代码片段依次apply到elem下执行。当然，这个过程只有在触发事件的时候，才会发生。

依照这个原理，那么要删除绑定的某个代码片段就很容易做到了，只需要从elem["_"+type]这个数组中把相应的代码片段删除即可。

解释一下为什么这个存放需要绑定代码片段的数组设置为elem的一个属性，其实理由很简单，就是不用声明一个全部变量（数组类型），来管理这个二维的数据结构，这样可以保证bindEvent,removeEvent的高度独立性。
