---
title: method_exists VS is_callable
date: 2011-08-13 21:53:01
tag: PHP
category: 开发
---

> 介绍method_exists和is_callable的区别，提醒大家在实际项目中不要犯错。

今天看到一篇介绍，关于 method_exist 的，我觉得自己也有必要记录一下。


在很多应用中，我们经常能够看到下面的这种用法。用来检查一个对象的一个方法是否存在：

```php
if ( method_exists ( $object, 'SomeMethod' )) {
	$object->SomeMethod($this,TRUE);
}
```


这段代码的目的非常容易理解，有一个对象为 $object，我们想知道他是否有一个方法为 SomeMethod 。如果有，就调用这个方法。


这个代码看起来很正确，而且在大部分的时候，工作的也非常好。但是如果这个 $object 对象的方法对于当前的运行环境是不可见的，程序还能正常运行吗？method_exists 正如这个函数名一样，只是对我们提供的类或对象检查是否有我们所期望的方法，如果有，就返回TRUE，如果没有，就返回FALSE，这里并没有考虑可见性的问题。所以，如果你恰好判断一个私有或者受保护的方法时，你能够得到一个正确的返回，但是执行的时候，会得到一个 “Fatal Error”。


正确的做法


其实上面一段代码的真正意图应该理解为：对于提供的类或者对象，我们能否在当前的作用域中调用他的 SomeMethod 方法。而这正是 is_callable() 存在的目的。


如何来做


is_callable 接收一个回调参数，可以指定一个函数名称或者一个包含方法名和对象的数组，如果在当前作用域中可以执行，就返回TRUE，如果不能就FALSE。

```php
if( is_callable( array($object,'SomeMethod'))) {
	$object->SomeMethod($this,TRUE);
}
```


下面是一个例子，用来说明 method_exists 和 is_callable 的区别：

```php
classFoo {
	publicfunctionPublicMethod(){}
	privatefunctionPrivateMethod(){}
	publicstaticfunctionPublicStaticMethod(){}
	privatestaticfunctionPrivateStaticMethod(){}
}

$foo=newFoo();

$callbacks=array(
	array($foo,'PublicMethod'),
	array($foo,'PrivateMethod'),
	array($foo,'PublicStaticMethod'),
	array($foo,'PrivateStaticMethod'),
	array('Foo','PublicMethod'),
	array('Foo','PrivateMethod'),
	array('Foo','PublicStaticMethod'),
	array('Foo','PrivateStaticMethod'),
);

foreach($callbacksas$callback){
	var_dump($callback);
	var_dump(method_exists($callback[0],$callback[1]));
	var_dump(is_callable($callback));
	echo str_repeat('-',40);
	echo'<br />';
}
```


执行这个例子，我们就能购清晰的看到两个函数间的差别。

**补充**


is_callable 还有其他的用法，例如不检查所提供的类或方法，只是检查函数或方法的语法是否正确。像 method_exists一样，is_callable 可以触发类的自动加载。


如果一个对象有魔术方法 __call，在进行方法判断的时候 method_exists 会返回FALSE ， 而is_callable 会返回TRUE。


如下例：

```php
classMethodTest {
	publicfunction__call($name,$arguments){
		echo'Calling object method'.$name.''.implode(',',$arguments);
		echo'<br />';
	}
}

$obj=newMethodTest();

$obj->runtest('in object context');
var_dump(method_exists($obj,'runtest'));
var_dump(is_callable(array($obj,'runtest')));
echo'<br />';
```

## 参考资料
1、[is_callable](http://php.net/manual/en/function.is-callable.php)
2、[method_exists](http://www.php.net/manual/en/function.method-exists.php)













