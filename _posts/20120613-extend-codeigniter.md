---
title: Codeigniter中对核心类的扩展
date: 2012-06-13 16:55:01
tag: 
---

Codeigniter框架提供了实现多个应用Application的方法，如参考资料[[2](http://codeigniter.com/user_guide/general/managing_apps.html)]中描述的，这种方法实际上是在网站目录下存在多个入口文件和Application文件夹的方式。这种方式实现有个缺点，加入我做了一个应用放在Application下，同时为这个应用做了一个后台放在Admin文件夹下，实际上Model里的模块是可以共用的，但是使用这种方式却不得不将Model做一份拷贝，当然我们也可以使用软链的方式来避免硬拷贝，但是用起来总是不爽。
所以一般我在开发后台的时候，喜欢在 controller 目录下建立一个单独的后台文件夹，例如 controller/admin/，这样就可以实现Model的共用。Controller中的文件需要一些公共的方法，在Codeigniter中，当我们需要在所有的控制器Controller中添加一些公共方法时，可以考虑对Controller进行扩展。例如用户登录的检查函数，具体的方法我们可以参考[[1](http://www.gregaker.net/2011/mar/18/extending-codeigniters-controller/)]中的描述。如果我的前台页面不需要检查登录，后台页面需要检查登录，使用这种方法就会有问题。那么我们能不能定义两个可被继承的Controller呢，例如一个叫做My_Controller一个叫做My_AdminController，很可惜的是不行。
扩展核心类的实现代码，位于System/Core/Codeigniter.php的第214行，代码如下：
1:if(file_exists(APPPATH.'core/'.$CFG->config['subclass_prefix'].'Controller'.EXT))
2:{
3:require APPPATH.'core/'.$CFG->config['subclass_prefix'].'Controller'.EXT;
4:}

由此可见，我们无法定义一个MY_Controller给前台调用，一个MY_AdminController给后台调用。
在这里只好使用一种变通的方法，通过url的segment来判断，代码如下：
1:classMY_Controller extends CI_Controller {
2:function__construct(){
3:parent::__construct();
4:if( $this->uri->segment(1) ==='admin' ){
5:...
6:}
7:}
8:}

希望在Codeigniter的3.0版本中能够有些改善。
适用的Codeigniter版本：2.0及以上。
参考资料：
1、[Extending Codigniter's Controller](http://www.gregaker.net/2011/mar/18/extending-codeigniters-controller/)
2、[Manage Applications](http://codeigniter.com/user_guide/general/managing_apps.html)
3、[Codeigniter extends core class](http://stackoverflow.com/questions/9348541/codeigniter-extends-core-class)
4、[Codeigniter uri](http://codeigniter.com/user_guide/libraries/uri.html)
5、[Codeigniter 3.0](http://codeigniter.com/nightly_user_guide/index.html)












