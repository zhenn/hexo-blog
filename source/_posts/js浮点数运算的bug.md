title: "js浮点数运算的bug"
date: 2011-04-17 20:02:21
categories: 技术
tags: javascript
---

老实说在今天之前，我对javascript的浮点数运算毫无认识，觉得应该和实际运算中一样，然而，有些事情往往会出人意料。

先看一组数据:

- 0.1 + 0.11 = 0.21000000000000002
- 0.2 * 3 = 0.6000000000000001
- 0.7 / 10 = 0.06999999999999999

<!--more-->

你相信吗？如果不信，那么立即打开firefox浏览器测试吧。有一点可以确信，你看到测试结果的表情，一定先是惊讶，后来是迷惑。

查看加法运算：

{% codeblock %}
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>test</title>
<style type="text/css">
body{line-height:24px;padding-left:40px;}
#msg{position:absolute;width:270px;height:100px;border:1px solid #f60;left:500px;top:100px;background:#f60;color:#fff;font-weight:700;text-align:center;}
span{color:red;}
</style>
</head>
    
<body>
<h1>浮点数加法</h1>
<div id="msg"></div>
<button type="button" id="btn1">点击使用原生方法进行计算</button>
<button type="button" id="btn2">点击使用自定义方法进行计算</button>
<button type="button" id="btn3">清空错误信息</button>
<div id="runResult"></div>
<script type="text/javascript">
    var result = document.getElementById('runResult');
    var msg = document.getElementById('msg');
    var btn1 = document.getElementById('btn1');
    var btn2 = document.getElementById('btn2');
    var btn3 = document.getElementById('btn3');
    
    function mul(num1,num2){
        var reg = /\./i;
        if(!reg.test(num1) && !reg.test(num2)){
            return num1 * num2;
        }
        var len = 0, str1 = num1.toString(), str2 = num2.toString();
        if(str1.indexOf('.')>=0){
            len += str1.split('.')[1].length;
        }
        if(str2.indexOf('.')>=0){
            len += str2.split('.')[1].length;
        }
        return Number(str1.replace('.','')) * Number(str2.replace('.','')) / Math.pow(10,len)
    
　　} 
    
    function add(num1,num2){
        var reg = /\./i;
        if(!reg.test(num1) && !reg.test(num2)){
            return num1 * num2;
        }
        var r1 = 0, r2 = 0, m;
        var str1 = num1.toString(), str2 = num2.toString();
        if(str1.indexOf('.')>-1){
            r1 = str1.split('.')[1].length;
        }
        if(str2.indexOf('.')>-1){
            r2 = str2.split('.')[1].length;
        }
        m = Math.pow(10,Math.max(r1,r2));
        return (mul(num1,m) + mul(num2,m)) / m;
    }
        
    function ownMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = i/10 + i/100;
            if(falsemsg.toString().length>6){
                count++;
            }
            arr.push(i + '/10+' + i + '/100 值为：<span>' + falsemsg + '</span><br>');
        }
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用原生方法进行1000次加法运算出现浮点数bug的次数为：" + count;
    };
    
    
    function myMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = add(i/10,i/100);
            if(falsemsg.toString().length>6){
                count++;
            }
            arr.push('add(' + i + '/10,' + i + '/100值为：<span>' + falsemsg + '</span><br>');
        }
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用自定义方法进行1000次加法运算出现浮点数bug的次数为：" + count;
    }
    
    btn1.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        ownMethod();
    };
    
    btn2.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        myMethod();
    };
    
    btn3.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
    };
        
</script>
</body>
</html>
{% endcodeblock %}

查看乘法运算：

{% codeblock %}
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>test</title>
<style type="text/css">
body{line-height:24px;padding-left:40px;}
#msg{position:absolute;width:200px;height:100px;border:1px solid #f60;left:400px;top:100px;background:#f60;color:#fff;font-weight:700;text-align:center;}
span{color:red;}
</style>
</head>
   
<body>
<h1>浮点数乘法</h1>
<div id="msg"></div>
<button type="button" id="btn1">点击使用原生方法进行计算</button>
<button type="button" id="btn2">点击使用自定义方法进行计算</button>
<button type="button" id="btn3">清空错误信息</button>
<div id="runResult"></div>
<script type="text/javascript">
    var result = document.getElementById('runResult');
    var msg = document.getElementById('msg');
    var btn1 = document.getElementById('btn1');
    var btn2 = document.getElementById('btn2');
    var btn3 = document.getElementById('btn3');
   
    function mul(num1,num2){
        var reg = /\./i;
        if(!reg.test(num1) && !reg.test(num2)){
            return num1 * num2;
        }
   
        var len = 0, str1 = num1.toString(), str2 = num2.toString();
        if(str1.indexOf('.')>=0){
            len += str1.split('.')[1].length;
        }
        if(str2.indexOf('.')>=0){
            len += str2.split('.')[1].length;
        }
        return Number(str1.replace('.','')) * Number(str2.replace('.','')) / Math.pow(10,len);
   
　　} 
   
    function ownMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = i/10*100;
            if(falsemsg.toString().length > 5){
                count++;
            }
            arr.push(i + '/10*100==<span>' + falsemsg + '</span><br>');
        }
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用原生方法进行1000次乘法运算出现浮点数bug的次数为：" + count;
    };
       
   
    function myMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = mul(i/10,100);
            if(falsemsg.toString().length > 5){
                count++;
            }
            arr.push('mul(' + i + '/10,100)==<span>' + falsemsg + '</span><br>');
        }
           
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用自定义方法进行1000次乘法运算出现浮点数bug的次数为：" + count;
    }
       
   
    btn1.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        ownMethod();
    };
   
    btn2.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        myMethod();
    };
   
    btn3.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
    };
       
</script>
</body>
</html>
{% endcodeblock %}

查看除法运算：

{% codeblock %}
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>test</title>
<style type="text/css">
body{line-height:24px;padding-left:40px;}
#msg{position:absolute;width:200px;height:100px;border:1px solid #f60;left:400px;top:100px;background:#f60;color:#fff;font-weight:700;text-align:center;}
span{color:red;}
</style>
</head>
  
<body>
<h1>浮点数除法</h1>
<div id="msg"></div>
<button type="button" id="btn1">点击使用原生方法进行计算</button>
<button type="button" id="btn2">点击使用自定义方法进行计算</button>
<button type="button" id="btn3">清空错误信息</button>
<div id="runResult"></div>
<script type="text/javascript">
    var result = document.getElementById('runResult');
    var msg = document.getElementById('msg');
    var btn1 = document.getElementById('btn1');
    var btn2 = document.getElementById('btn2');
    var btn3 = document.getElementById('btn3');
  
    function mul(num1,num2){
        var reg = /\./i;
        if(!reg.test(num1) && !reg.test(num2)){
            return num1 * num2;
        }
        var len = 0, str1 = num1.toString(), str2 = num2.toString();
        if(str1.indexOf('.')>=0){
            len += str1.split('.')[1].length;
        }
        if(str2.indexOf('.')>=0){
            len += str2.split('.')[1].length;
        }
        return Number(str1.replace('.','')) * Number(str2.replace('.','')) / Math.pow(10,len)
  
　　} 
  
    function div(num1,num2){
        var reg = /\./i;
        if(!reg.test(num1) && !reg.test(num2)){
            return num1 * num2;
        }
        var len1 = 0, len2 = 0;
        var str1 = num1.toString(), str2 = num2.toString();
        //计算位数差
        if(str1.indexOf('.')>-1){
            len1 = str1.split('.')[1].length;
        }
        if(str2.indexOf('.')>-1){
            len2 = str2.split('.')[1].length;
        }
        return mul(Number(str1.replace('.','')) / Number(str2.replace('.','')),Math.pow(10,len2-len1)); 
    }
      
    function ownMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = i/10/10;
            if(falsemsg.toString().length > 5){
                count++;
            }
            arr.push(i + '/10/10==<span>' + falsemsg + '</span><br>');
        }
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用原生方法进行1000次除法运算出现浮点数bug的次数为：" + count;
    };
  
  
    function myMethod(){
        var falsemsg = null;
        var count = 0;
        var arr = new Array;
        for(var i=0;i<1000;i++){
            falsemsg = div(i/10,10);
            if(falsemsg.toString().length > 5){
                count++;
            }
            arr.push('div(' + i + '/10,10)=<span>' + falsemsg + '</span><br>');
        }
        result.innerHTML = arr.join('');
        msg.innerHTML = "利用自定义方法进行1000次除法运算出现浮点数bug的次数为：" + count;
    }
  
    btn1.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        ownMethod();
    };
  
    btn2.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
        myMethod();
    };
  
    btn3.onclick = function(){
        result.innerHTML = '';
        msg.innerHTML = '';
    };
      
</script>
</body>
</html>
{% endcodeblock %}

** 对于这样的结果，我不知道是怎么产生的，但是既然是bug，就一定是可以hack的，思路很简单，先把所有的运算数转成int型（表达不准确，javascript的没有int、float之分），计算出两个运算数的小数点后的位数，再进行适当的运算，就可以得到原始的我们期望看到的结果了。
**


