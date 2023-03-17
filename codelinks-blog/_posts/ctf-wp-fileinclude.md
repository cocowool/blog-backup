---
title: [CTF WP][攻防世界]文件包含 
date: 2023-03-17 08:13:16
keywords:
description: 本文是攻防世界 Web 题目文件包含的 Write Up，欢迎大家交流讨论。
---

## 题目介绍

这是攻防世界的一道 Web 题目，从题目描述和给出的源代码看，这是一个php的文件包含题目。

```php
<?php
highlight_file(__FILE__);
    include("./check.php");
    if(isset($_GET['filename'])){
        $filename  = $_GET['filename'];
        include($filename);
    }
?>
```

## WriteUp

看到有 include 函数，尝试了使用 php://filter 伪协议。

![image-20230317081611716](ctf-wp-fileinclude/image-20230317081611716.png)

## 参考资料

1. [Multibyte String](https://www.php.net/manual/en/mbstring.supported-encodings.php)
1. [详解php://filter以及死亡绕过](https://blog.csdn.net/woshilnp/article/details/117266628)
1. [泰山杯：文件包含](https://blog.csdn.net/qq_36618918/article/details/128674773)
