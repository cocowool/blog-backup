---
title: This关键字的一些更新的理解
date: 2008-12-30 09:46:01
tag: 
---

this是Javascript中一个非常重要的关键字，正确的使用this能够使我们的程序效率得到提升。之前，对于this的认识还存在一些误区，今天看了一篇文章之后又有了一些深入的了解。

this的所有者。
一般情况下，this是指向正在执行的函数本身，或者是，指向函数所属的对象。如
function dosomething(){
this.style.color = 'red';

}

函数执行时，this指向的就是window对象。而：
var dosomething = function(){
this.style.color = 'red';

}

this是指向dosomething这个对象的；
很不幸，window对象没有style属性，所以这个dosomething这个函数直接执行就会导致错误，我们如果想正确应用this，我们就需要将函数复制到对象的属性上去。

例如：element.onclick = dosomething;
总结一下，通过复制正确使用this的例子有：

element.onclick = dosomething;
element.addEventListener('click',dosomething,false);

element.onclick = function(){ this.style.color = 'red'; };
<element onclick='javascript:this.style.color = red;' />

通过直接引用使用this的例子：
element.onclick = function(){ dosomething(); };

element.attachEvent('onclick',dosomething);
<element onclick = "dosomething();" />

这样的引用，this都会被指向window对象而导致，执行失败，所以在平时的应用中是要引起注意的。













