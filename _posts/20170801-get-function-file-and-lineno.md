---
title: PHP获取指定函数定义在哪个文件中及行号
date: 2017-08-01 10:28:01
tag: 
---

当调试开源的代码时，希望查看某个函数的定义，那么就需要定位其位置。特别是有的项目中，函数会有多个地方都有定义，那么如果我想知道当前调用的这个函数是在哪里定义的，可以用下面这个方法。
```php
function function_dump($funcname) {  
    try {  
        if(is_array($funcname)) {  
            $func = new ReflectionMethod($funcname[0], $funcname[1]);  
            $funcname = $funcname[1];  
        } else {  
            $func = new ReflectionFunction($funcname);  
        }  
    } catch (ReflectionException $e) {  
        echo $e->getMessage();  
        return;  
    } www.jbxue.com  
    $start = $func->getStartLine() - 1;  
    $end =  $func->getEndLine() - 1;  
    $filename = $func->getFileName();  
    echo "function $funcname defined by $filename($start - $end)\n";  
}
```
> Zend studio 这样的 IDE 支持跳转到函数定义。













