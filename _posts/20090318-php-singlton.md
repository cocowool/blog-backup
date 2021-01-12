---
title: 设计模式－PHP实现单件模式的几种方式
date: 2009-03-18 17:12:01
tag: 
keywords: php, 单件模式, 设计模式
description: 单件模式是我们在开发中经常用到的一种设计模式，利用PHP5面向对象的特性，我们可以很容易的构建单件模式的应用，本文介绍单件模式在PHP中的几种实现方法。
---

单件模式是我们在开发中经常用到的一种设计模式，利用PHP5面向对象的特性，我们可以很容易的构建单件模式的应用，下面是单件模式在PHP中的几种实现方法：

```php
class Stat{
    static $instance = NULL;
    
    static function getInstance(){
        if(self::$instance == NULL){
            self::$instance = new Stat();
        }
        
        return self::$instance;
    }
    
    private function __construct(){
    }
    
    private function __clone(){
    }    
    
    function sayHi(){
        return "The Class is saying hi to u ";
    }
}


echo Stat::getInstance()->sayHi();
```

这是一种最通常的方式，在一个getInstance方法中返回唯一的类实例。

对这里例子稍加修改，便可以产生一个通用的方法，只要叫道任何你想用到单件的类里，就可以了。

```php
class Teacher{
    function sayHi(){
        return "The teacher smiling and said 'Hello '";
    }
    
    static function getInstance(){
        static $instance;
        
        if(!isset($instance)){
            $c = __CLASS__;
            $instance = new $c;
        }
        
        return $instance;
    }
}

echo Teacher::getInstance()->sayHi();
```

最后一种是提供一个singleton类，然后通过调用getInstance方法，可以为任何一个类生产出一个实例来。

```php
class singleton{
    function getInstance($class){
        static $instances = array();
        if(!array_key_exists($class,$instances)){
            $instances[$class] = &new $class;
        }
        $instance = $instances[$class];
        
        return $instance;
    }
}

class People{
    function sayHi(){
        return 'Hello i am a people?';
    }
}

echo "<br />";
echo singleton::getInstance('People')->sayHi();
```

通过这三种方法，我们可以很容易的应用单件模式，如果能够结合工厂模式，将使我们的编程变得更有条理和效率。













