---
title: IE下尚未实现错误的原因
date: 2008-12-23 15:49:01
tag: 
---

IE下JS执行，提示“尚未实现”的错误，在firebug中没有任何错误提示。打开IE就遇到该死的“尚未实现错误”，根据IE中提示的位置找过去也没有发现任何错误，看来IE的报错定位也不太准确。在网上找到的原因：错误在于window.onload= myFunc(var1,var2);IE的window.onload函数中不支持参数调用，虽然函数会照样执行，但是却会出现报错，影响后续脚本的继续执行，下面是两种简单而有用的解决办法：

1. 再写一个函数，譬如function loadFunc(){ myFunc(var1,var2) },然后window.onload = loadFunc;
2. 使用匿名函数。onload =function(){myFunc(var1,var2)}












