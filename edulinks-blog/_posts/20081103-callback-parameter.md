---
title: 如何向回调函数中传入其他参数
date: 2008-11-03 23:44:01
tag: 
keywords: javascript, 回调函数, 回调函数传参
description: 本文总结介绍了向回调函数中传入参数的方法。
---

如何向回调函数中传参数

最近写JS经常会因为向回调函数中传参而头疼，今天总结一下向回调函数中传参的方法，以后的应用中就不用在到处去找了。

首先构建一个需要向回调函数中传入参数的典型应用。在一个页面中产生了一系列的向Ajax Proxy的请求，传入的是一个ID，根据ID返回了不同的内容值，我们需要把这些内容打印在页面上，同时给页面元素赋予ID，这个时候就需要向回调函数中传入ID，以产生带ID的页面元素。

第一种方法就是使用全局变量，能够被函数和回调函数同时访问。这种方法虽然不够优雅，但是确实能够完成任务。

这种方法在传入单个的变量时没什么问题，但是当我们在一个循环的结构中，不断的传入变量到回调函数，这个时候传入的变量会采用最后一次传入的变量值，这就与我们预想的结果有了出入。

第二种办法是使用Closure，这种方法更加优雅一些。通过使用Closure，我们通过匿名函数来重新包装返回的对象，同时将需要传入的参数做为新的属性传给回调函数。

第三种方法假设你需要为你的回调函数使用不同的签名，例如Ajax.Net的专家们允许你在回调中使用额外的参数，如果你想从一个换到另外一个并且保持兼容性的话，就要用到下面的写法：

第四种办法其实就是Closure的一种变体，你不需要显示的声明一个回调函数，而是使用一个匿名函数直接进行你所需要的处理。

如何向回调函数中传参数

总结一下：向回调函数中传入参数的终极办法其实就是利用Closure，这个看来是唯一可行而且比较优雅的方法，下面将Closure的写法列在下面：
```javascript
 var callback = {
        success:function(data){
            var item = document.createElement("li");
            item.id = data.id;
            item.innerHTML = "The id is : " + data.id;
            item.innerHTML += " The value is : " + data.value;
            
            var parent = document.getElementById("result");
            parent.appendChild(item);
        },
        failure:function(){
            alert('failure:');
        }
    }

    xhr = new QueuedHandler();
    for(var i=0;i<20;i++){
        xhr.request('get','ajaxproxy.php?id='+i,function(data){        //第二种办法：利用Closure
            data = eval('(' + data + ')');
            data.id = i;
            callback.success(data);
        });

    }
```













