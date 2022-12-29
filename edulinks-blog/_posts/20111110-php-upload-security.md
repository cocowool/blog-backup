---
title: PHP文件上传中的安全问题
date: 2011-11-10 23:31:01
tag: 
keywords: PHP上传文件, PHP安全, PHP
description: 使用PHP进行开发时，经常遇到文件上传的场景。其中会隐藏很多我们平时注意不到的安全问题。
---

在使用PHP进行开发时，经常遇到文件上传的场景。其中会隐藏很多我们平时注意不到的安全问题，我总结了一下，主要有几个方面：

1、检查用户传来的文件名，避免 ../etc/passwd 这样的探测
2、有些应用使用了用户名做为路径名，那么也需要对用户名进行详细的检查
3、有些人提到了文件上传的Dos攻击，这个我觉得主要是依靠网络层面去解决，禁掉频繁访问的IP，但是对于僵尸网络的攻击，貌似没什么好的办法

下面这篇文章中介绍的内容有些老，因为现在已经不是PHP3和PHP4的那个时代，虽然如此，关于文件上传中的安全处理，我们还是需要多加注意。

正常的表单没有提供文件上传的功能，所以在 RFC 1867 中提出了《HTML中基于表单的文件上传》这个规范。看下面的列子：
```html
<FORM METHOD="POST" ENCTYPE="multipart/form-data">
  <INPUT TYPE="FILE" NAME="hello">
  <INPUT TYPE="HIDDEN" NAME="MAX_FILE_SIZE" VALUE="10240">
  <INPUT TYPE="SUBMIT">
</FORM>
```

上面的代码让用户从本地机器选择一个文件，当点击提交后，文件就会被上载到服务器。这显然是很有用的功能，但是PHP的响应方式使这项功能变的不安全。当PHP第一次接到这种请求，甚至在它开始解析被调用的PHP代码之前，它会先接受远程用户的文件，检查文件的长度是否超过 “$MAX_FILE_SIZE” 变量定义的值，如果通过这些测试的话，文件就会被存在本地的一个临时目录中。因此，攻击者可以发送任意文件给运行PHP的主机，在PHP程序还没有决定是否接受文件上载时，文件已经被存在服务器上了。这里我就不讨论利用文件上载来对服务器进行DOS攻击的可能性了。

让我们考虑一下处理文件上载的PHP程序，正如我们上面说的，文件被接收并且存在服务器上（位置是在配置文件中指定的，一般是/tmp），扩展名一般是随机的，类似“phpxXuoXG”的形式。PHP程序需要上载文件的信息以便处理它，这可以通过两种方式，一种方式是在PHP 3中已经使用的，另一种是在我们对以前的方法提出安全公告后引入的。

但是，我们可以肯定的说，问题还是存在的，大多数PHP程序还是使用老的方式来处理上载文件。
PHP设置了四个全局变量来描述上载文件，比如说上面的例子：

```php
$hello = Filename on local machine (e.g "/tmp/phpxXuoXG")
$hello_size = Size in bytes of file (e.g 1024)
$hello_name = The original name of the file on the remote system (e.g "c:\\temp\\hello.txt")
$hello_type = Mime type of uploaded file (e.g "text/plain")
```

然后PHP程序开始处理根据“$hello”指定的文件，问题在于“$hello”不一定是一个PHP设置的变量，任何远程用户都可以指定它。如果我们使用下面的方式：
http://vulnhost/vuln.php?hello=/etc/passwd&hello_size=10240&hello_type=text/plain&hello_name=hello.txt
就导致了下面的PHP全局变量（当然POST方式也可以（甚至是Cookie））：

```php
$hello = "/etc/passwd"
$hello_size = 10240
$hello_type = "text/plain"
$hello_name = "hello.txt"
```

上面的表单数据正好满足了PHP程序所期望的变量，但是这时PHP程序不再处理上载的文件，而是处理“/etc/passwd”（通常会导致内容暴露）。这种攻击可以用于暴露任何敏感文件的内容。

PHP手册中提到“PHP 遵从大多数服务器系统中关于文件和目录权限的安全机制。这就使管理员可以控制哪些文件在文件系统内是可读的。必须特别注意的是全局的可读文件，并确保每一个有权限的用户对这些文件的读取动作都是安全的。” 我们知道，/etc/passwd 是全局可读的…。

参考资料：
1、PHP中文件上传中的安全问题
2、RFC1867 HTML中基于表单的文件上传
3、PHP手册，文件系统安全
4、PHP安全基础 表单及URL上传攻击