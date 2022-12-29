---
title: 使Windows Vista 也支持cUrl扩展
date: 2008-11-12 13:46:01
tag: 
keywords: php, curl, curl扩展
description: 扩展不生效的小问题，是因为缺少了其他的动态链接库。
---

我们都知道PHP的扩展在php.ini中只要将extension前的注释去掉就可以，可是我在Vista下使用php_curl.dll时，却怎么都无法正常运行，到底是什么原因呢，今天终于恍然大悟。

首先，如果打开了php_curl.dll，在phpinfo中看不到应该出现的，那么就要检查一下错误日志了，可以看到：

> PHP Warning:  PHP Startup: Unable to load dynamic library 'E:""php""ext""php_ldap.dll' - "xd5"xd2"xb2"xbb"xb5"xbd"xd6"xb8"xb6"xa8"xb5"xc4"xc4"xa3"xbf"xe9"xa1"xa3"r"n in Unknown on line 0


这就是说php_curl.dll无法正常加载，这是由于在win下，有些php扩展是需要其他的动态链接库支持的。

然后，重新查看了一下PHP的官方文档，原来这里有详细的描述。
http://www.php.net/manual/en/install.windows.extensions.php

有一个列表，详细的列出了所需的DLL，比如cUrl

可是只把列表中的两个文件拷贝到system32中并不能解决问题，还需要 ntwdblib.dll的支持curl才能正常工作。这样就可以在vista下使用curl的函数了，困扰了一上午的问题终于解决。













