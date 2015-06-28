title: "基于window.name的跨域解决方案"
date: 2011-07-02 13:43:40
categories: 技术
tags: ['javascript','跨域']
---

### 一，原理：
iframe在加载新页面时，name值是保持不变的，由此可以重定向iframe的引用地址，由外域转到本域。

### 二，方法：
设置请求目标域页面的window.name，通过iframe引用至本域，由于js受到浏览器同源策略限制，无法跨域通信，取不到iframe中传过来 的window.name设置值，所以需要在本域设置一个代理页面（空文件即可），监听iframe的load事件，一旦加载完成，设置iframe指向 本域下的代理页面，此时就不存在跨域问题了，同时js可以取得iframe传输过来的跨域数据，即window.name值。

以下是封装的组件：

<!--more-->

{% codeblock %}
/**
 * 构造函数（利用window.name跨域访问,保证目标页面设置window.name）
 * @class windowName    
 */
var windowName = function(){
    this.init.apply(this,arguments);
};
windowName.prototype = {
    /**
     * 初始化
     * @param url{string} 目标域url地址
     * @param config{object} 配置项
     * @配置说明:proxy{string} 本域下的代理页面地址   
     *        callback(function) 处理数据的回调
     * @return void
     */
    init: function(url,config){
        var that = this;
        that.status = false;
        that.data = '';
        var config = that.checkInterface(config);
        that.getData(url,config);
    },
    /**
     * 验证接口,构建正确的参数形式
     * @param obj{object} 配置项 
     * @return object   
     */
    checkInterface: function(obj){
        return {
            proxy: obj.proxy || 'proxy.html',
            callback: obj.callback || new Function
        };
    },
    /**
     * 对iframe的onload实现事件监听
     * @param frame{dom} 对象
     * @param callback(function) 回调
     * @return void
     */
    frameLoad: function(frame,callback){
        if(frame.attachEvent){
            frame.attachEvent('onload',function(){
                callback();
            });
        }else{
            frame.onload = function(){
                callback();
            }
        }
    },
    /**
     * 获取跨域数据,并执行回调
     * @param url{string} 目标域url地址
     * @param config{object} 配置项
     * @配置说明:proxy{string} 本域下的代理页面地址
     *        callback(function) 处理数据的回调
     * @return void
     */
    getData: function(url,config){
        var that = this;
        var frame = that.frame = document.createElement('iframe');
        frame.style.visibility = 'hidden';
                frame.style.height = '0';
        document.body.insertBefore(frame,null);
        that.frameLoad(frame,function(){
            if(that.status){
                that.data = frame.contentWindow.name;
                that.clearFrame();
                config.callback(that.data);
            }else{
                that.status = true;
                frame.contentWindow.location.href = config.proxy;
            }
        });
        frame.src = url;
    },
    /**
     * 清除iframe
     * @param 
     */
    clearFrame: function(){
        var that = this;
        that.frame.parentNode.removeChild(that.frame);
    }
};
{% endcodeblock %}

使用方法：

{% codeblock %}
var btn = document.getElementById('btn'),
var result = document.getElementById('result');
btn.onclick = function(){
    new windowName('http://third-party-sources.com',{
        //本域下的代理页面（空页面即可）
        proxy: 'test.html',
        callback: function(o){
            result.innerHTML = o;
        }
    });
};
{% endcodeblock %}

更多关于跨域的知识，请猛击：[这里](http://research.microsoft.com/en-us/um/people/helenw/papers/subspace.pdf)

update in 2011-08-30 by zhenn:

{% codeblock %}
windowName.prototype.getData = function(){
    var that = this;
    var frame = that.frame = document.createElement('iframe');
    /**
     * 用visibility代替display
     * 避免使用此方法做跨域iframe高度自适应时的bug
     * 当iframe设置display:none
     * 设置目标页面window.name = document.body.offsetHeight -> window.name = 0
     */
    //frame.style.display = 'none';
    frame.style.visibility = 'hidden';
    frame.style.height = '0';
   
    document.body.insertBefore(frame,null);
    that.frameLoad(frame,function(){
        if(that.status){
            that.data = frame.contentWindow.name;
            that.clearFrame();
            config.callback(that.data);
        }else{
            that.status = true;
            frame.contentWindow.location.href = config.proxy;
        }
    });
    frame.src = url;    
};
{% endcodeblock %}


