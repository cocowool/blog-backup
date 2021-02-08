---
title: Value Object Pattern 值对象模式
tags:
keywords: 设计模式, 值对象模式
description: 
category: 
---

值对象模式的最大特点就是保证在程序运行或者变量复制过程中，对象的值可以在对象初始化之后，一直保持不变。

PHP5中的例子：
```php
Class Dollar {
	Protected $amount;
	Public function __construct( $amount ){
		$this->amount = $amount;
	}
 
	Public function getAmount(){
		Return $this->amount;
	}
 
	Public function add($dollar){
		Return new Dollar($this->amount + $dollar->getAmount);
	}
}
```

初始化之后 Dollar 内的元素就保持不变，增加的时候，始终是在固定基数的基础上增加。因为 Value Object Pattern 的这些特性，也可以在这个模式中封装一些逻辑操作。
