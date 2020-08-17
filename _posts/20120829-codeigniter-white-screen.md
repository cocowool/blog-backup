---
title: CodeIgniter 2.1.0 的白屏错误调试
date: 2012-08-29 16:23:01
tag: 
---

今天在配置一个CodeIgniter 2.1.0时，遇到白屏，系统报500错误，但是Apache的错误日志中看不到任何错误信息输出。
经过简单的定位后，发现是由于在autoload中配置了自动加载database造成的，但是之前的版本用着都没问题呀，这个版本出这个问题，甚是奇怪。
系统的一些相关信息：
OS:Cent OS 5.4
Apache:2.2.4
PHP:5.2.17
Mysql:5.0.77
通过不断的die测试，定位到system/database/drivers/mysql/mysql_driver.php文件中，发现mysql的调用函数都用了@将错误信息隐藏了，难怪白屏，什么信息都没有。最后测试到，问题出在下面这个函数上：
1:function db_set_charset($charset, $collation)
2:{
3:if( ! isset($this->use_set_names))
4:{
5:// mysql_set_charset() requires PHP >= 5.2.3 and MySQL >= 5.0.7, use SET NAMES as fallback
6:$this->use_set_names = (version_compare(PHP_VERSION,'5.2.3','>=') && version_compare(mysql_get_server_info(),'5.0.7','>=')) ? FALSE : TRUE;
7:}
8:
9:if( !function_exists('mysql_set_charset') ){
10:$this->use_set_names = TRUE;//解决PHP MYSQL模块的BUG问题
11:}
12:
13:if($this->use_set_names === TRUE)
14:{
15:return@mysql_query("SET NAMES '".$this->escape_str($charset)."' COLLATE '".$this->escape_str($collation)."'", $this->conn_id);
16:}
17:else
18:{
19:return@mysql_set_charset($charset, $this->conn_id);
20:}
21:}

在我这个版本的php中，不存在mysql_set_charset这个函数，但是判断的结果是需要这个函数，所以执行到这里的时候就发生错误了。
但是按照PHP的版本来说，这个函数应该是存在的，可能是编译的时候一些参数选择的问题造成了这个错误。
参考资料：
1、[CodeIgniter Blank Page Fix: Blank webpage when setting up CodeIgniter, PHP, MySQL, and Apache](http://www.roryhansen.ca/2008/10/29/fix-blank-webpage-problems-when-first-setting-up-code-igniter-php-mysql-and-apache/)
2、[Mysql set charset](http://cn2.php.net/mysql-set-charset)












