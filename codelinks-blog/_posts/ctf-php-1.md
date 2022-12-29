---
title: CTF中的PHP知识点
date: 2022-12-29 21:46:39
keywords: ctf
description:  
---

## 文件读取

```php
<?php  
// $filename 可以用 GET / POST 方式传入
if( isset($text) && (file_get_contents($filename,'r')==="hello ctf")){

}
```

当遇到 file_get_contents($file_name) 代码时，尝试通过 GET / POST 方法构建一个文件。

### data 伪协议

```php
data://text/plain,hello ctf
data://text/plain;base64,aGVsbG8gY3Rm
```



## 参考资料

1. [CTF include](https://blog.csdn.net/qq_61774705/article/details/126323696)
