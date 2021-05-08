---
title: jQuery的attr方法处理checkbox的问题
date: 2017-09-06 15:05:01
tag: 
keywords: jQuery, jquery attr, jquery checkbox
description: 使用了 jQuery 1.10 的版本，想实现 checkbox 的全部选中和全部取消选中，使用了 attr 的方法。
---


### 现象
使用了 jQuery 1.10 的版本，想实现 checkbox 的全部选中和全部取消选中，使用了 attr 的方法，如下：
```javascript
$(elem).attr(“checked”)
```
测试过程中发现，第一次从未选中状态变为选中，再从选中变为未选中，是可以的。但是第二次界面上就没有任何变化了，但是查看元素，发现 checked 属性实际上已经改变了。这是什么情况呢？

### 原因
看一下官方对这个函数的解释
> **.attr()**
Get the value of an attribute for the first element in the set of matched elements or set one or more attributes for every matched element.

然后官方还特别注明了
> As of jQuery 1.6, the .attr() method returns undefined for attributes that have not been set.**To retrieve and change DOM properties such as the checked, selected, or disabled state of form elements, use the .prop() method.**

文档还特别对 attr() 和 prop() 进行了对比，总体来说 jQuery 1.6 版本之后，为了保证函数在不同环境下表现的一致性，建议对于获取 DOM 元素的固有属性，使用 prop() 方法，如果需要修改个性化（自定义）的属性字段，则需要使用 attr 方法。
![](/20170906-jquery-attr/39469-20170906150427897-1646491621.png)
关于 checked 属性，还特别说了一下，我也不知道如何用汉字区分 attribute 和 property，还是摘抄下来吧。

> Nevertheless, the most important concept to remember about the checked attribute is that it does not correspond to the checked property. The attribute actually corresponds to the defaultChecked property and should be used only to set the initial value of the checkbox. The checked attribute value does not change with the state of the checkbox, while the checked property does. Therefore, the cross-browser-compatible way to determine if a checkbox is checked is to use the property

```javascript
if ( elem.checked )
    if ( $( elem ).prop( "checked" ) )
    if ( $( elem ).is( ":checked" ) )
```

参考资料：
1、[jQuery attr](http://api.jquery.com/attr/)
2、[jQuery prop](http://api.jquery.com/prop/)
3、[W3C Forms](https://www.w3.org/TR/html401/interact/forms.html#h-17.4)












