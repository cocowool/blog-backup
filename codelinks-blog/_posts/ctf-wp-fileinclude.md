---
title: [CTF WP][攻防世界]文件包含 
date: 2023-03-17 08:13:16
keywords:
description:
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



![image-20230317081611716](ctf-wp-fileinclude/image-20230317081611716.png)

## 参考资料

1. []()
