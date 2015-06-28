title: "object数据存储的无序性"
date: 2011-05-10 13:49:34
categories: 技术
tags: javascript
---

object作为javascript的一种数据存储格式，采用哈希表的存取方式，即一个key对应唯一的值，这样的特性，有时候给开发带来很大的便利，比如当我们需要获得一系列不能重复的数字时，可以做如下处理：

{% codeblock %}
var randomOne = function(){
    var i = 0 , obj = {};
    while(i < 6){
        var num = Math.ceil(10 * Math.random());
        if(obj[num]) continue;
        obj[num] = num;
        i ++;
    }
    return obj;
}
var o = randomOne();
for(var i in o){
    alert(o[i]);
}
{% endcodeblock %}

<!--more-->

产生的随机数，分别作为key和value存入对象obj中，这样新产生的随机数在存入对象之前，只需先点对点的查找此数在对象中是否有相应的键值对，就可以确保数字的唯一性，而这样往往是比在数组中检索数据要快的多。

这一切看上去很完美，既满足了需求，又提高了性能，而事实上，一朵看上去很美丽的花往往是有毒的，一不小心就可以致人于死地。同样的，使用object存储数据给我们带来便捷的同时，也有一定的风险，正如你看到的文章标题，object数据存储时没有顺序的，当业务的需求对数据的顺序有严格要求的时候，这恰恰就是致你于死地的那滴毒液。

为了给上述的观点提供足够的佐证，请分别在webkit浏览器和非webkit浏览器中执行randomOne，横向对比两者的运算结果，就会发现在webkit中，遍历对象，输出数据的顺序是从小到大，而在非webkit中，则是按照存贮的先后顺序逐个输出。在这一点上，webkit事实上是把带有可转为number类型的key序列当做数组来处理的，以下更有说服力的代码：

{% codeblock %}
var o = {};
o['s'] = '先存储的数据';
o[1] = '后存储的数据';
for(var i in o){
    alert(o[i])
}
{% endcodeblock %}

webkit中的结果：'后存储的数据' -> '先存储的数据'，非webkit中的结果：'先存储的数据' -> '后存储的数据'。

我想以上足够证明本文的观点了，下面的demo是在项目中遇到的应用场景，即随机产生一注双色球选号。

项目需求：

- 红球选号范围01-33，选号不能重复
- 篮球选号范围01-16

{% codeblock %}
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>demo</title>
<script src="http://files.cnblogs.com/zhenn/yui-min.js" type="text/javascript"></script>
<style type="text/css">
*{margin:0;padding:0;}
h1{font-size:24px;}
#wrapper{width:800px;margin:50px auto;color:#555;}
ul{font:40px/1.5 arial;font-weight:700;margin-top:20px;float:left;}
ul li{float:left;margin-right:20px;list-style:none;color:#f00;}
ul li.blue{color:blue;}
button{border:1px solid #d5d5d5;margin:20px 250px 0 0;float:right;}
h4{clear:left;font-size:24px;}
</style>
</head>
  
<body>
<div id="wrapper">
    <h1>object存贮数据的无序性</h1>
    <ul>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li class="blue"></li>
    </ul>
    <button class="btn">重选一注</button>
    <h4>分别用webkit浏览器和非webkit浏览器打开，查看数字排序</h4>
</div>
<script>
    var randowNumber = {
        state: 0,
        refresh: document.getElementById('wrapper').getElementsByTagName('button')[0],
        boxes: document.getElementById('wrapper').getElementsByTagName('li'),
        init: function(){
            var that = this;
            that.ballRandom();
            that.refresh.onclick = function(){
                that.ballRandom();
            };
        },
        //产生一个随机字符串,如:01 22 23 12 13 02 07
        randomOne: function(){
            var that = this;
            var i = 0 , obj = {} , str = '';
            while(i < 7){
                if(i < 6){
                    var num = Math.ceil(33 * Math.random());
                }else{
                    var num = Math.ceil(16 * Math.random());
                }
                if(obj[num]) continue;
                obj[num] = that.padding(num);
                i ++;
            }
            for(var j in obj){
                str += obj[j] + ' ';
            }
            return str.substring(0,str.length - 1);
        },
        //填充字符串
        padding: function(str){
            if(typeof str != 'number') return;
            if(str < 10){
                str  = '0' + str;
            }else{
                str = str.toString();
            }
            return str;
        },
        //重新渲染dom
        render: function(){
            var that = this;
            var arr = that.randomOne().split(' ');
            for(var i=0,len=that.boxes.length;i<len;i++){
                that.boxes[i].innerHTML = arr[i];
            }
        },
        //随机数产生的过程并渲染在dom上
        ballRandom: function(){
            var that = this;
            if(!that.state){
                that.timer = setInterval(function(){
                    if(that.state == 50){
                        clearInterval(that.timer);
                        that.state = 0;
                    }else{
                        that.render();
                        that.state ++;
                    }
                },20);  
            }
        }
    };
    randowNumber.init();
</script>
</body>
</html>
{% endcodeblock %}

实际上，在webkit浏览器中，严重违背了项目需求，bug严重程度应该是A级，因为这影响了此功能的可用性，导致用户无法购买彩票。

所以，在对有数据顺序有要求的场景下，应该避免使用object来存取数据，尽量用array来替代，尽管这样会在一定程度上有损前端性能，但和破坏功能可用性比起来，还是不值一提的。
