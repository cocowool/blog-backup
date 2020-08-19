---
title: 利用PUT方式上传文件的方法研究
date: 2010-05-31 14:09:01
tag: 
---

虽然没有POST方法使用广泛，但是PUT方法却是向服务器上传文件最有效率的方法。POST上传文件时，我们通常需要将所有的信息组合成 multipart 传送过去，然后服务器再解码这些信息，解码过程则必不可少的会消耗内存和CPU资源，这种现象在上传大文件时尤其明显。而PUT方法则允许你通过与服务器建立的socket链接传递文件的内容，而不附带其他的信息。
最近一个项目上需要利用这种方式来进行文件的上传，下面介绍一下在 Apache + PHP 的环境下如何进行PUT方式的文件上传。
Apache 有一个模块 mod_actions，先看看官方的说明：
> This module has two directives. The```Action```directive lets you run CGI scripts whenever a file of a certain[MIME content type](http://cnblogs.com/glossary.html#mime-type)is requested. The```Script```directive lets you run CGI scripts whenever a particular method is used in a request. This makes it much easier to execute scripts that process files.

也就是说，这个模块可以指定对于特定 MIME 类型的文件处理，或者对于特定脚本的请求进行指定的处理。我用到的就是 Script 这个选项。
在Apache 配置文件的 Directory 中指定
> Script PUT /receive.php

这个含义就是，对于所有对服务器的PUT请求，都交给根目录下的 receive.php 去处理，当然我们也可以选择 perl 或者其他的CGI脚本来进行处理。
接下来就是这个 receive.php 脚本的编写了，他的主要任务就是将请求的文件写到指定的位置
> <?php
/**
* Process The PUT File, receive and move a file to corresponsed location
* Created by shiqiang<[cocowool@gmail.com](mailto:cocowool@gmail.com)> at 2010-05-24
*
**/
class Receive {
var $default_log_file = "logs/error.log";
var $default_server_info = "logs/server.log";
var $default_header_info = "logs/header.log";
var $default_prefix = "/data1/vhosts";    //Default project location prefix;
var $default_module = "test.cn";
var $max_filesize = 2048000;
var $request_uri;
function Receive(){
$this->request_uri = $_SERVER['REQUEST_URI'];
}
function saveFile(){
//receive data and save
$putdata = fopen("php://input", "r");
$path = $this->getPath($this->request_uri);
$fp = fopen($path, 'w');
while($data = fread($putdata, 1024) ){
fwrite($fp, $data);
}
fclose($fp);
fclose($putdata);
//Log The filesize check and limit check
if( filesize($path) != $_SERVER['CONTENT_LENGTH'] ){
$this->errorLog( "[warn] " . date("Y-m-d H:i:s")  . " The file's ($path) size dosen't match Server Filesize = " . filesize($path) . "; Put Filesize = " . $_SERVER['CONTENT_LENGTH']. "\r\n" );
header('HTTP/1.1 526 Receive Data Error');
}
if( filesize($path) > $this->max_filesize ){
$this->errorLog( "[warn] " . date("Y-m-d H:i:s")  . " The file's ($path) size exceed the system limit");
}
}
//Log Error Info
function errorLog( $info ){
$f = fopen($this->default_log_file, 'a+');
fwrite($f, $info);
flcose($f);
}
function serverLog(){
$f = fopen($this->default_server_info, 'w');
$info = $_SERVER;
$str = "The Last Request Server Info:\r\n";
foreach ($info as $key => $value){
$str .= "$key = $value\r\n";
}
$str .= $this->getPath($this->request_uri) . "\r\n";
$str .= "PHP_UPLOADED_FILE_NAME=" . $PHP_UPLOADED_FILE_NAME . "\r\n";
fwrite($f , $str);
fclose($f);
}
//Log the Request Header info
function headerLog(){
$f = fopen($this->default_header_info, 'w');
$info = get_headers();
$str = "The Last Request header Info:\r\n";
foreach ($info as $key => $value){
$str .= "$key = $value\r\n";
}
fwrite($f , $str);
fclose($f);
}
//get the path where the file should be
function getPath($uri){
$module = $this->defalt_module;    //Default storage module
$referer = $this->request_uri;
preg_match('/(?<=\/)(.*?)(?=\/)/s', $referer, $match);
if( !empty($match) && !empty($match[0]) ){
$module = $match[0];
}
$path = $this->default_prefix;
$path .= '/' . $module . '/htdocs';
$fullpath = substr($uri, strlen($match[0]) + 1, strlen($uri) );
$arr = explode('/', ltrim($fullpath, '/'));
foreach($arr as $v){
if( !strstr($v, '.') ){
$path .= '/' . $v;
//exec("echo $path >> dir.txt");
if( !is_dir($path) ){
//For php > 5.0
//mkdir($path, "0766", true);
mkdir($path, 0766);
}
}else{
$path .= '/' . $v;
}
}
return $path;
}
}
$instance = new Receive();
$instance->serverLog();
//$instance->headerLog();
$instance->saveFile();
?>

这个脚本，使用PHP手册中的接收PUT方式的方法，详细的使用，GOOGLE的时候，并没有找到很多，所以可能对于错误情况，考虑的也不是很全面，如果有使用过这个方法的欢迎和我讨论。
Technorati 标签:[PHP](http://technorati.com/tags/PHP),[PUT](http://technorati.com/tags/PUT),[Script](http://technorati.com/tags/Script)
参考资料：
1、[PUT Upload](http://upload.thinfile.com/docs/put.php)
2、RFC 2616












