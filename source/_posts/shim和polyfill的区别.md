title: "shim和polyfill的区别"
date: 2016-05-18 15:27:23
categories: 技术
tags: shim
---

在Javascript相关文章中，经常出现两个词，分别是shim和polyfill，两者含义非常相近，都有封装、兼容的意思，但又不尽相同。

<!--more-->

shim是将不同的api封装成统一的，比如JQuery的$.ajax封装了标准的XMLHttpRequest和IE使用的ActiveXObjct去创建xhr对象。polyfill则特指封装后的统一的api`遵循标准`，就上例而言其做法是在IE中创建XMLHttpRequest对象，内部实现则用ActiveXObjct。

为了方便对比，约定shim的api由开发者自由制定，而非遵循规范。因此，polyfill是shim的一种。



