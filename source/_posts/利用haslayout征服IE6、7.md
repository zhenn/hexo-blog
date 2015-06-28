title: "利用haslayout征服IE6、7"
date: 2008-05-01 11:02:22
categories: 技术
tags: css
---

[haslayout](https://msdn.microsoft.com/en-us/library/ie/ms530764%28v=vs.85%29.aspx)是`Windows Internet Explorer`渲染引擎的一个内部组成部分。在IE中，一个元素要么自己对自身的内容进行计算大小和组织，要么依赖于父元素来计算尺寸和组织内容。为了调节这两个不同的概念，渲染引擎采用了hasLayout的属性，属性值可以为true || false。

在html元素中，负责组织自身内容的元素将默认有一个布局，主要包含以下元素： body, html, table, tr, th, td, img, hr, input, button, file, select, textarea, fieldset, marquee, frameset, frame, iframe, objects, applets, embed。而div, ul, li, h1~h6之流，在默认情况下，是没能获得layout的，按照微软给出的解释，这是出于对“性能和简洁”和间接的考虑,如果所有元素都默认有布局，会对性能和内存使用上产生有害的影响。
<!--more-->

乍一看这段话，似乎不知所云，但是如果说在IE中稀奇古怪的bug和这个所谓的haslayout有关，或许可以吸引你的些许注意力，相信每个有过前端开发经历的人，都会对IE系列浏览器中形形色色的诸多bug搞的焦头烂额。针对这些bug，我们知道一种或者几种不同的解决办法（方法当然是有巧拙之分的），但是对于问题的根源，我们却经常不去深究，也不知道如何去更深一层的认识它！

当网页在IE中有异常表现时，可以尝试激发haslayout来看看是不是问题所在，最常用的方法是给某元素css设定zoom:1或者width/height属性，其次再考虑其他属性，因为使用这两种方法能在不改变现有环境的条件下激发元素的haslayout。

事实上大部分的IE显示错误，都可以通过激发元素的haslayout属性来修正，能激发元素haslayout的属性值除zoom和width/height之外还包括：

- display: inline-block
- float: (left 或 right)
- position: absolute
- writing-mode: tb-rl

另外本文中提到的IE，只包括IE6和IE7，至于新版本的IE8，不在范围之内，貌似IE8已经解决了haslayout带来的诸多麻烦，具体情况不详！
