---
title: Apache的Charset设置
date: 2009-02-03 16:26:01
tag: 
---

今天从这篇文章中学习了apache关于defaultcharset的设置和优先级的问题。
1.页面没有指定charset ， Apache配置defaultcharset gbk , 页面文件编码是utf-8。
执行结果是页面乱码。这个几乎是肯定的，在页面没有meta指明charset，而服务器的defaultcharset又没有被注释掉，可以肯定页面是会乱码的，这个时候服务器的设置生效；

2.页面指定charset为utf-8,  Apache配置defaultcharset  gbk. 页面文件是utf-8。
执行结果是页面乱码。这个就验证了当服务器的defaultcharset打开时，会忽略掉页面的编码设置；
3.PHP header申明charset为utf8,  Apache配置defaultcharst gbk,页面文件编码是utf8。
执行结果是页面正常。这个说明header中指定的信息的优先级要高于服务器及浏览器的设置；
4.Apache设置DefaultCharset off。
页面显示正常。


最后，在apache的手册中找到结论。
![](./2009-02-03-1383291/None.gif)AddDefaultCharset 指令
![](./2009-02-03-1383291/None.gif)说明    当应答内容是text/plain或text/html时，在HTTP应答头中加入的默认字符集
![](./2009-02-03-1383291/None.gif)语法    AddDefaultCharset On|Off|charset
![](./2009-02-03-1383291/None.gif)默认值    AddDefaultCharset Off
![](./2009-02-03-1383291/None.gif)作用域    server config, virtual host, directory, .htaccess
![](./2009-02-03-1383291/None.gif)覆盖项    FileInfo
![](./2009-02-03-1383291/None.gif)状态    核心(C)
![](./2009-02-03-1383291/None.gif)模块    core
![](./2009-02-03-1383291/None.gif)
![](./2009-02-03-1383291/None.gif)当且仅当应答内容是text/plain或text/html时，此指令将会在HTTP应答头中加入的默认字符集。理论上这将覆盖在文档体中通过<meta>标 签指定的字符集，但是实际的行为通常取决于用户浏览器的设置。AddDefaultCharset Off 将会禁用此功能。 AddDefaultCharset On 将启用Apache内部的默认字符集iso-8859-1 。您也可以指定使用在IANA注册过的字符集名字 中的另外一个charset 。比如说：
![](./2009-02-03-1383291/None.gif)
![](./2009-02-03-1383291/None.gif)AddDefaultCharset utf-8





如果服务器和页面都没有指定编码，我想这时编码是由浏览器的默认编码来确定的，这时Firefox和IE就会发生区别，当然是指安装在中文系统里的浏览器，如果系统不同我想结果还会有差异。













