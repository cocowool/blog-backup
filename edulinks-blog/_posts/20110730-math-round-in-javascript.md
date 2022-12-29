---
title: JavaScript中进行四舍五入
date: 2021-03-19 18:17:30
tags:
keywords: javascript, javascript 四舍五入
description: 本文介绍了用javascript进行四舍五入的四种方法。
---

用Javascript取float型小数点后两位，例22.127456取成22.13,如何做？
1. 最笨的办法
```javascript
function get(){
    var s = 22.127456 + "";
    var str = s.substring(0,s.indexOf(".") + 3);
    alert(str);
}
```

2. 正则表达式效果不错
```javascript
<script type="text/javascript">
  onload = function(){
      var a = "23.456322";
      var aNew;
      var re = /([0-9]+\.[0-9]{2})[0-9]*/;
      aNew = a.replace(re,"$1");
      alert(aNew);
  }
</script>
```

3. 他就比较聪明了.....
```javascript
<script>
  var num=22.127456;
  alert( Math.round(num*100)/100);
</script>
```

4.会用新鲜东西的朋友....... 但是需要 IE5.5+才支持。
```javascript
<script>
  var num=22.127456;
  alert( num.toFixed(2));
</script>
```