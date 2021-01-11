---
title: IE下JS文件失效问题总结
date: 2009-02-16 13:16:01
tag: 
keywords: ie, javascript
description: 
---

今天遇到了一个很诡异的问题，Firefox下好好的页面，在IE下却接二连三的报错，这到底是什么地方产生的错误。
经过一上午的仔细排查，发现了真凶原来是IE对于对象和数组定义的严格限制。
在JS中，我们一般定义数组和对象都如下：

var arr = [
arr1,
arr2,
arr3,
arr4
];

对象：
var obj = {
param:one,
param:two,
param:three
};
如果最后的一项多了一个“,”，那么在IE中就会产生错误，从而最终导致引用的JS文件失效。
例如：

<script type='text/javascript' src='a.js'></script>
function a(){
...

}
var obj = {

param:a,
param:b,
param:c,

<script type='text/javascript' src='b.js'></script>

a();

alert(obj);
这个时侯就会出现函数调用的错误，如果文件小函数少的时候比较容易发现，如果函数很多，而恰恰对象的定义放在文件末尾的话，那就比较痛苦了。
记下这个问题，在今后要格外的注意呢














