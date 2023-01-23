

本文记录一些 CTF 试题中 Python 相关的内容。

如果通过 Response 信息判断 Web 服务是 Python 的情况，可以使用 `{{1}}` 的方式探测是否可以进行注入。

* 模块注入
* 模板注入

```http
/error?msg={{1}}
/error?msg={{handler.settings}}
/error?msg={{config}}
```

## SSTI

SSTI 是服务端模板注入的缩写（Server Side Template Injection）

## 系统的敏感文件

Linux 中一些敏感的系统文件。

```sh
/etc/passwd
/etc/environment
/etc/hostname
/etc/hosts
/proc/self/cmdline
```



## Tornado

在 tornado 框架模板中，存在一些可以快速访问的对象，

* `handler.settings` ，handler 指向 RequestHandler，而 RequestHandler.settings 又指向 self.application.settings，所以handler.settings 就指向 RequestHandler.application.settings，这里面包含了一些环境变量

## 参考资料

1. [Python Template Injection](https://blog.csdn.net/qq_45774670/article/details/110223398)
1. [buuctf easy-tornado](https://blog.csdn.net/ANYOUZHEN/article/details/124983143)
1. [一文掌握CTF中Python全部考点](https://www.freebuf.com/column/232197.html)
1. [警惕Python中的路径穿越](https://blog.csdn.net/qq_36078992/article/details/122070641)
1. [[83：CTF夺旗-Python考点SSTI&反序列化&字符串](https://www.cnblogs.com/zhengna/p/15798046.html)](https://www.cnblogs.com/zhengna/p/15798046.html)