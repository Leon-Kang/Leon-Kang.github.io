---
layout: post
author: Leon-Kang
title: retain与copy的区别
date:  2016-01-17
categories: iOS
tag: iOS
---

* 了解这二者就必须先了解一下深浅复制有什么区别。

>   浅 复制：在复制操作时，对于被复制的对象的每一层复制都是指针复制。
   
>   深 复制：在复制操作时，对于被复制的对象至少有一层复制是对象复制。

>   完全 复制：在复制操作时，对于被复制的对象的每一层复制都是对象复制。

* 总而言之就是深复制会复制对象本身，而浅复制则只停留在指针层面，所以相对应的是：

> retain：始终是浅复制。引用计数每次加一。返回对象是否可变与被复制的对象保持一致。

> copy：对于可变对象为深复制，引用计数不改变;对于不可变对象是浅复制，
         引用计数每次加一。始终返回一个不可变对象。

> mutableCopy：始终是深复制，引用计数不改变。始终返回一个可变对象。

* 然后再从索引次数和释放顺序的角度来看：

> copy:建立一个索引计数为1的对象，然后释放旧对象 。

> retain:释放旧的对象，将旧对象的值赋予输入对象，再提高输入对象的索引计数为1。  

参考[http://www.cnblogs.com/langtianya/p/3722129.html](http://www.cnblogs.com/langtianya/p/3722129.html)

[http://www.cnblogs.com/csj007523/archive/2012/07/23/2605662.html](http://www.cnblogs.com/csj007523/archive/2012/07/23/2605662.html)

