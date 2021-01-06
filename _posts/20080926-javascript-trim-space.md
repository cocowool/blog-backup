---
title: JavaScript 去除空格函数
date: 2008-09-26 14:08:01
tag: 
---

利用正则去除字符串前后空格的方法：

```javascript
function trim(strToTrim){
    return strToTrim.replace(/^\s+|\s+$/g,"")
}
function ltrim(strToTrim){
    return strToTrim.replace(/^\s+/,"");
}
function rtrim(strToTrim){
    return strToTrim.replace(/\s+$/,"");
}
//我们可以把这些添加为String对象的子方法：
String.prototype.trim = function(){
    return this.replace(/^s+|\s$/g,"");
}
String.prototype.ltrim = function(){
    return this.replace(/^s+/,"");
}
String.prototype.rtrim = function(){
    return this.replace(/\s+$/,"");
}
```

上面这种方法支持 JavaScript 1.2+ 以及 Jscript3.0+，对于目前大多数的浏览器（4.0+）都可以支持，如果你需要支持更老的浏览器，可以使用下面这个例子，这能够去掉空格、空行、TAB、回车以及换页。

```javascript
var trim4old = {
    ltrim:function(strToTrim){
        for(var k=0; k < strToTrim.length && this.isWhiteSpace(strToTrim.charAt(k));k++);
        return strToTrim.substring(k,strToTrim.length);
    },
    rtrim:function(strTomTrim){
        for(var j = strTomTrim.length - 1;j>=0 && this.isWhiteSpace(strTomTrim.charAt(j));j--);
        return strTomTrim.substring(0,j+1);
    },
    trim:function(strToTrim){
        return this.rtrim(this.ltrim(strToTrim));
    },
    isWhiteSpace:function(strTomTrim){
        var whitespaceChars = "\t\n\r\f";
        return (whitespaceChars.indexOf(strTomTrim) != -1);
    }
}
```