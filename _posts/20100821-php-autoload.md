---
title: PHP中Autoload使用中的一些问题
date: 2010-08-21 16:22:01
tag: 
---

PHP中提供了Autoload来帮助我们方便的进行文件的包含，但是autoload并非想象的那样能够处理所有的情况，今天就来记录一下前几天遇到的autoload存在的一些问题。
**为什么要使用 Autoload**。
在PHP中使用类时，我们必须在使用前加载进来，不管是通过 require 的方式还是 include 的方式，但是会有两个问题影响我们做出加载的决定。
首先是不知道这个类文件存放在什么地方，另外一个就是不知道什么时候需要用到这个文件。特别是项目文件特别多时，不可能每个文件都在开始的部分写很长一串的 require ….
在PHP5之后，我们可以通过[__autoload](http://uk2.php.net/autoload)来解决这个问题。 而且在PHP5.1之后，还提供了[spl_autoload_register()](http://uk.php.net/manual/en/function.spl-autoload-register.php)来提供更完善的加载机制。
通过阅读了[Autoloading in PHP](http://wp.drapeko.com/2009/03/autoloading-in-php/)这篇文章，我理解的 Autoload 的加载机制，当通过 new 来实例化一个类时，PHP会通过定义的__autoload 函数加载相应的文件，如果这个类文件使用了 extends 或者 implements 需要用到其他的类文件，php会重新运行 autoload 去进行类文件的查找和加载，如果发生了两次对同一类文件的请求，就会报错。原文作者提供了三个很有趣的例子来说明这个问题，可以通过[这里](http://drapeko.com/store/autoload_in_php/autoload_examples.rar)下载源码查看。
一般情况下，有很多种方法来解决加载时到相应位置查找文件的方法。用的最多的就是指定特定的命名标准。
**Zend的方法**。
zend推荐了一种最流行的办法，在文件名中包含路径。例如下面的例子：
> // Main.class
function __autoload($class_name) {
$path = str_replace('_', DIRECTORY_SEPARATOR, $class_name);
require_once $path.'.php';
}

$temp = new Main_Super_Class();

所有的下划线都会被替换成路径中的分隔符，上例中就会去 Main/Super/Class.php文件
这种方法的缺点是在编码过程中，我们必须明确的知道代码文件应当所处的位置，而且由于
将文件路径硬编码在了类名中，如果需要修改文件夹的结构时，我们必须手工修改所有的类名。
**'Include All’方法**
如果是在一个开发环境中，并且对于速度不是很在意的话，使用这个方法是非常方便的。通过将所有类文件放在一个或几个特定文件夹中，然后通过遍历的方式查找加载。
例如：
> <?php
$arr = array (
'Project/Classes',
'Project/Classes/Children',
'Project/Interfaces'
);
foreach($arr as $dir) {
$dir_list = opendir($dir);
while ($file = readdir($dir_list)) {
$path = $dir.DIRECTORY_SEPARATOR.$file;
if(in_array($file, array('.', '..')) || is_dir($path))
continue;
if (strpos($file, ".class.php"))
require_once $path;
}
}
?>


**关联文件和位置**
另外一个方法是在类文件和他的位置之间建立关联的配置文件，例如：
> // configuration.php
array_of_associations = array(
'MainSuperClass' = 'C:/Main/Super/Class.php',
'MainPoorClass' = 'C:/blablabla/gy.php'
);

调用的文件
> <?php
require 'autoload_generated.php';
function __autoload($className) {
global $autoload_list;
require_once $autoload_list[$className];
}
$x = new A();
?>

当然，如果文件特别多的时候，维护起来会是一件麻烦事，但是与在类名中硬编码位置，哪个更好呢? :)
我们当然不希望手工来维护这个列表，那么可以使用自动生成这个文件来实现，这个对应关系的文件可以是php\xml\json等等。原文的作者实现了一个这样的工具，仔细考虑一下的话，这个不是很难实现，原文作者甚至发展了一个小型的 Autoload 框架，值得学习。


参考资料：
1、[Autoloading in PHP](http://wp.drapeko.com/2009/03/autoloading-in-php/)
2、[Autoloading Classes](http://uk2.php.net/autoload)
3、[Autoload problem with static variables](http://bugs.php.net/31562)
4、[Class autoloades earlier when using APC](http://pecl.php.net/bugs/bug.php?id=14066)
5、[PHP AutoLoad Best Practices](http://ditio.net/2008/11/13/php-autoload-best-practices/)
Technorati 标签:[PHP](http://technorati.com/tags/PHP),[AUTOLOAD](http://technorati.com/tags/AUTOLOAD),[ZEND](http://technorati.com/tags/ZEND),[Name Standard](http://technorati.com/tags/Name+Standard)












