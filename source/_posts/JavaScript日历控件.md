title: "JavaScript日历控件"
date: 2010-04-12 12:37:21
categories: 技术
tags: javascript
---

由于项目需要，写了个日历控件，仿照windows的日历写的，可以添加备忘录，备忘录操作所存取数据，没有数据库的支持，只在js中建立一个数组来存储，故而在不刷新页面的情况下，可以进行添加查看备忘录操作，如需和服务器进行交互，使用ajax也十分的方便！

![](/img/5.jpg)
<!--more-->

代码实现：

{% codeblock %}
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>JavaScript日历控件</title>
<style type="text/css">
*{ padding:0; margin:0;}
h1{ text-align:center; margin-top:50px; }
#content{ width:400px; margin:30px auto; -webkit-box-shadow:0 0 8px #333; -moz-box-shadow:0 0 8px #333; padding:30px; overflow:hidden;}
.timemain{ width:350px; margin:0 auto;  overflow:hidden;}
.timemain .selectDate{ height:20px;}
.timemain .selectDate select{ float:left; width:160px;}
.timemain .selectDate em{ float:left; color:#000; margin-left:6px; width:160px; height:17px; *height:20px; border:1px solid #a5acb2; padding-left:4px; font-style:normal;}
.timemain .selectDate .btn{ float:left; width:15px; margin:2px 0 0 2px; display:inline;}
.timemain .timeForm{ border:1px solid #d5d5d5; margin-top:9px; font-size:14px; padding-bottom:3px;}
.timemain .timeForm .title th{ background:#bfcddb; color:#fff;}
.timemain .timeForm td{ cursor:pointer;}
.timemain .timeForm td.current{ background:#ccc; color:#fff;}
.timemain .memo{ margin-top:10px;}
.timemain .memo input{ width:342px; height:80px; border:1px solid #d5d5d5; font-size:12px; font-family:"宋体"; line-height:16px; padding:3px; color:#555;}
#save{ float:right; margin-top:12px; cursor:pointer;}
#copyright{ width:460px; margin:30px auto;}
</style>
</head>
  
<body>
<h1>JavaScript日历控件</h1>
<div id="content">
    <form name="date" method="post" action="">
    <div class="timemain">
        <div class="selectDate">
            <select name="selectMonth">
                <option value="1">一月</option>
                <option value="2">二月</option>
                <option value="3">三月</option>
                <option value="4">四月</option>
                <option value="5">五月</option>
                <option value="6">六月</option>
                <option value="7">七月</option>
                <option value="8">八月</option>
                <option value="9">九月</option>
                <option value="10">十月</option>
                <option value="11">十一月</option>
                <option value="12">十二月</option>
            </select>
            <em id="yearInfo"></em>
            <div class="btn"><img src="http://images.cnblogs.com/cnblogs_com/zhenn/283408/r_beforebtn.jpg" alt="" id="prevYear" /><img src="http://images.cnblogs.com/cnblogs_com/zhenn/283408/r_afterbtn.jpg" alt="" id="nextYear" /></div>
        </div>
        <div class="timeForm" id="timeForm"></div>
        <div class="memo"><input type="text" name="memo" value="添加备忘录" /></div>
        <img id="save" src="http://images.cnblogs.com/cnblogs_com/zhenn/283408/r_savabtn.jpg" alt="" />
    </div>
    </form>
</div>
<div id="copyright"><a href="http://www.cnblogs.com/zhenn/">振之博客</a> 版权所有 转载请说明出处</div>
<script>
/*注册事件监听器*/
function addEvent(elem,eventType,fn){
    if(!elem.addEventListener&&!elem.attachEvent){
        return false;
    }else if(elem.addEventListener){   
        /*如果是w3c浏览器*/
        elem.addEventListener(eventType,fn,false);
    }else if(elem.attachEvent){        
        /*如果是ie*/
        elem.attachEvent("on"+eventType,fn);
    }
}
  
addEvent(window,"load",function(){
    /*输出表格*/
    var timeForm = document.getElementById("timeForm");
    timeForm.innerHTML = '<table width="100%" border="0" cellspacing="0" cellpadding="0" style="margin-bottom:3px;"><tr class="title"><th height="28" align="center" valign="middle">日</th><th height="28" align="center" valign="middle">一</th><th height="28" align="center" valign="middle">二</th><th height="28" align="center" valign="middle">三</th><th height="28" align="center" valign="middle">四</th><th height="28" align="center" valign="middle">五</th><th height="28" align="center" valign="middle">六</th></tr></table>';
    var objTable = document.createElement("table");
    objTable.setAttribute("width","100%");
    objTable.setAttribute("border","0");
    objTable.setAttribute("cellspacing","0");
    objTable.setAttribute("cellpadding","0");
    for(var i=0;i<6;i++){
        var tr = objTable.insertRow(0);
        for(var j=0;j<7;j++){
            var td = tr.insertCell(0);
        }
    }
    timeForm.appendChild(objTable);
    var tds = objTable.getElementsByTagName("td");
    for(var i=0;i<tds.length;i++){
        tds[i].setAttribute("align","center");
        tds[i].setAttribute("valign","middle");
        tds[i].setAttribute("height",27);
    }
      
      
      
                                          
    var prevYear = document.getElementById("prevYear"); 
    var nextYear = document.getElementById("nextYear");
    var yearInfo = document.getElementById("yearInfo");
    var selectMonth = document.date.selectMonth;
      
    var savebtn = document.getElementById("save");
    var memo = document.date.memo;
    /*用于存放备忘录的数组，数组的每个元素都是一个对象{}*/
    var memos = new Array();
      
  
    /*获得当前日期的相关参数,并且把数据初始化到各个表单中*/
    var date = new Date();
    var year = date.getFullYear();
    var month = date.getMonth() + 1;
    var dateCurrent = date.getDate();
    var day = date.getDay();
    yearInfo.innerHTML = year;
    var option = selectMonth.options;
    for(var i=0;i<option.length;i++){
        if(parseInt(option[i].value)==month){
            option[i].selected = true;
        }
    }
    writedatePostion(parseInt(year),parseInt(selectMonth.options[selectMonth.selectedIndex].value),tds);
    for(var i=0;i<tds.length;i++){
        if(tds[i].innerHTML ==dateCurrent){
            tds[i].className = "current";
        }
    }
      
    /*选择年份*/
    prevYear.onclick = function(){
        yearInfo.innerHTML = (parseInt(yearInfo.innerHTML)-1).toString();
        writedatePostion(parseInt(yearInfo.innerHTML),parseInt(selectMonth.options[selectMonth.selectedIndex].value),tds);
        for(var i=0;i<tds.length;i++){
            tds[i].className = "";
            if(tds[i].innerHTML == dateCurrent){
                tds[i].className = "current";   
            }
        }
    }
    nextYear.onclick = function(){
        yearInfo.innerHTML = (parseInt(yearInfo.innerHTML)+1).toString();
        writedatePostion(parseInt(yearInfo.innerHTML),parseInt(selectMonth.options[selectMonth.selectedIndex].value),tds);
        for(var i=0;i<tds.length;i++){
            tds[i].className = "";
            if(tds[i].innerHTML == dateCurrent){
                tds[i].className = "current";   
            }
        }
    }   
      
    /*选择月份*/
    selectMonth.onchange = function(){
        writedatePostion(parseInt(yearInfo.innerHTML),parseInt(selectMonth.options[selectMonth.selectedIndex].value),tds);
        for(var i=0;i<tds.length;i++){
            tds[i].className = "";
            if(tds[i].innerHTML == dateCurrent){
                tds[i].className = "current";   
            }
        }
    }
      
      
    /*判断是否是瑞年*/
    function isLeapYear(iYear){
        if(iYear%4==0&&iYear%100!=0){
            return true;
        }else{
            if(iYear%400==0){
                return true;
            }else{
                return false;
            }
        }
    }
      
      
    /*获得某年某月的天数并且得出和星期的对应关系*/
    function writedatePostion(year,month,domArr){
        /*先初始化清除所有显示天数*/
        for(var i=0;i<domArr.length;i++){
            domArr[i].innerHTML = "";   
        }
          
        var nDate = new Date();
        var daynum;
        if(month==1||month==3||month==5||month==7||month==8||month==10||month==12){
            daynum = 31;
        }else if(month==4||month==6||month==9||month==11){
            daynum = 30;
        }else if(month==2&&isLeapYear(year)){
            daynum = 29;
        }else{
            daynum = 28;
        }
        nDate.setFullYear(year);
        nDate.setMonth(month-1);
        nDate.setDate(1);
        switch(nDate.getDay()){
            case 0:
                for(var i=0;i<daynum;i++){
                    domArr[i].innerHTML = i+1
                }
                break;
              
            case 1:
                for(var i=0;i<daynum;i++){
                    domArr[i+1].innerHTML = i+1
                }
                break;
              
            case 2:
                for(var i=0;i<daynum;i++){
                    domArr[i+2].innerHTML = i+1
                }
                break;
              
            case 3:
                for(var i=0;i<daynum;i++){
                    domArr[i+3].innerHTML = i+1
                }
                break;
                  
            case 4:
                for(var i=0;i<daynum;i++){
                    domArr[i+4].innerHTML = i+1
                }
                break;
                  
            case 5:
                for(var i=0;i<daynum;i++){
                    domArr[i+5].innerHTML = i+1
                }
                break;
                  
            case 6:
                for(var i=0;i<daynum;i++){
                    domArr[i+6].innerHTML = i+1
                }
                break;
        }
    }
      
      
    /*添加备忘录*/
    savebtn.onclick = function(){
        var date;
        if(memo.value == ""||memo.value=="添加备忘录"){
            alert("你还没有添加备忘录，无需保存！");
            return false;
        }
        for(var i=0;i<tds.length;i++){
            if(tds[i].className.indexOf("current")>=0){
                date = tds[i].innerHTML;    
            }
        }
        var o = {
            year: yearInfo.innerHTML,
            month: selectMonth.options[selectMonth.selectedIndex].value,
            date: date,
            _memo: memo.value
        }
        memos.push(o);  
        alert("添加备忘录成功！");
    }
      
    /*选择具体某一天的响应程序*/
    for(var i=0;i<tds.length;i++){
        tds[i].onclick = function(){
            memo.value = "添加备忘录";
            if(this.innerHTML!=""){
                for(var j=0;j<tds.length;j++){
                    //zhennJs.removeClass(tds[j],"current");    
                    tds[j].className = "";
                }
                this.className = "current";
                for(var i in memos){
                    if(yearInfo.innerHTML==memos[i].year&&selectMonth.options[selectMonth.selectedIndex].value==memos[i].month&&this.innerHTML==memos[i].date){
                        memo.value = "今日备忘：\n"+memos[i]._memo;
                    }
                }
            }
        }
    }
    /**备忘录操作所存取数据，没有数据库的支持。
    *只在js中建立一个数组来存储，故而在不刷新页面的情况下，可以进行操作
    *如需和服务器进行交互，使用ajax也十分方便
    */
      
});
</script>
</body>
</html>
{% endcodeblock %}




