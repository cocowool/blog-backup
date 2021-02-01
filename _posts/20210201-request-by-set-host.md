---
title: 指定HOST访问特定网址
date: 2021-02-01 13:16:03
tags:
keywords: php, linux, curl, hosts
description: 在测试过程中，如果需要修改某个域名的地址指向，除了修改/etc/hosts文件，还可以通过编程的方式实现。
---

平时测试过程中，经常会需要编辑HOST文件来访问特定的服务器。实际上，这个过程也可以在代码中完成。这个实现方式的根本，就是在HTTP请求的Header中，指定请求的HOST。

1、使用CURL
```sh
$ curl --silent -H "Host:house.baidu.com" "60.28.244.21/xxx/xxx/x.php"
```

2、使用PHP的CURL函数指定
```php
   //httpHeader   设置的 http head 参数 数组形式 如 array('Host: [client.51.com](http://client.51.com/)')
   function comm_curl_request($url,$postString='',$httpHeader='')
   {
      $ch = curl_init();
      curl_setopt($ch,CURLOPT_URL,$url);
      curl_setopt($ch,CURLOPT_POSTFIELDS,$postString);
      curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
      curl_setopt($ch,CURLOPT_USERAGENT,$_SERVER['HTTP_USER_AGENT']);
      if(!empty($httpHeader) && is_array($httpHeader))
      {
         curl_setopt($ch, CURLOPT_HTTPHEADER, $httpHeader);
      }
      $data = curl_exec($ch);
      $info = curl_getinfo($ch);
      //var_dump($info);
      curl_close($ch);
      return $data;
   }
```

3、使用file_get_contents函数
```php
   <?php

   $opts = array('http' => array( 'header' => 'Host: house.baidu.com',)); 
   $context = stream_context_create($opts); 
   $result = file_get_contents('[http://60.28.244.21/a.php](http://10.6.6.6/a.php)', false, $context); 

   echo $result;
 ?> 
```

参考资料：
1、[CURL请求指定HOST的URL](http://blog.csdn.net/gggxin/archive/2009/07/30/4394978.aspx)
2、[不用设置HOST，访问测试的HTTP接口](http://phpor.net/blog/post/772/)