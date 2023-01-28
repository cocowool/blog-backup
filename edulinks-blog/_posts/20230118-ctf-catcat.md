---
title: 攻防世界CTF题目catcat-new Writeup
date: 2023-01-18 19:30:41
keywords:
description:
---

最近攻防世界新放出了一道难度为2的Web题目 catcat-new。本文记录了整个学习摸索的过程，欢迎大家批评指正。

## 探索阶段

这是一道 Web 题目，打开后提供了一个展示猫咪图片的网站，可以查看每只猫咪的详细信息。

使用 xray 做了初次的扫描。

```sh
```

提示有路径穿越漏洞，可以实现任意文件的读取。尝试了在根目录下找 flag 文件没有结果。

然后查看了一些系统的敏感文件，例如

* /etc/passwd
* /etc/environment
* /etc/hostname
* /etc/hosts

后来在这个环节停顿了好几天，也曾尝试 SSTI 注入，发现不存在这个问题，这是后来获取到网站源码后才知道的。

后来查网上的资料，开始探索 `/proc` 目录，在 `/proc/self/cmdline` 中发现了关键要素。

```sh
# 访问 http://61.147.171.105:56391/info?file=../../proc/self/cmdline
# 看到了
b'python\x00app.py\x00'
```

因为摸索到这是一个容器环境，猜测脚本可能部署在 `/app` 目录下，于是通过下面几个链接，找到了全部的源码。

* /info?file=../../app/app.py
* /info?file=../../app/cat.py
* /info?file=../../app/templates/index.html
* /info?file=../../app/templates/detail.html

> 源代码放到了我的 Github 中，大家可以访问参考。

## 逐渐深入

因为尝试 SSTI 无法成功，就继续摸索文件路径穿越可能存在的问题，尝试更多的文件，逐步摸清出了这是一个 docker 应用，运行了 python 程序提供 Web 服务。

能够看到程序代码后，做了3种尝试。

第一种是想尝试Flask SESSION伪造，通过修改本地Cookie去绕过SESSION认证，但是因为SECRET_KEY采取了 uuid4 函数生成，本地生成的和服务器生成的不一致，这个尝试失败。

第二种网上搜到一些资料，提到 uuid4 使用的是随机数算法，存在重复的可能性，因此尝试去碰撞，本地写了脚本不断尝试，始终没有成功。后来咨询单位的 CTF 大拿，提醒说这个方法基本没听到过，遂放弃。

第三种看到应用启动的时候会先生成 flag 文件，读取内容后再删除。想到之前遇到过一个比手速的题目，尝试重启应用后用脚本快速获取内容。因为重建场景每次的端口号都不一样，始终也没有成功。

```python
import os
import uuid 
from flask import Flask, request, session, render_template, Markup 
from cat import cat

flag = ""
app = Flask( __name__, static_url_path='/', static_folder='static' )
app.config['SECRET_KEY'] = str(uuid.uuid4()).replace("-", "") + "*abcdefgh"

# http://61.147.171.105:52059/info?file=../../app/flag
# 50734,52160,56860,54194
if os.path.isfile("/flag"):
    flag = cat("/flag") 
    os.remove("/flag")
    
@app.route('/', methods=['GET'])
def index():
    detailtxt = os.listdir('./details/') 
    cats_list = []
    for i in detailtxt:
        cats_list.append(i[:i.index('.')])

    return render_template("index.html", cats_list=cats_list, cat=cat)


@app.route('/info', methods=["GET", 'POST'])
def info():
    filename = "./details/" + request.args.get('file', "")

    start = request.args.get('start', "0")
    end = request.args.get('end', "0")
    name = request.args.get('file', "")[:request.args.get('file', "").index('.')]

    return render_template("detail.html", catname=name, info=cat(filename, start, end))

@app.route('/admin', methods=["GET"])
def admin_can_list_root():
    print(app.config['SECRET_KEY'])
    print(session)
    session['admin']=1

    if session.get('admin') == 1: 
        flag = "aaaaa"
        return flag
    else:
        session['admin'] = 0
        return "NoNoNo"
        
if __name__ == '__main__': 
    app.run(host='0.0.0.0', debug=False, port=5637)
```

> 刚开始抓到 b'abc\nabc' 样式的内容，我还手工把代码格式调整好，看到其他人的 WP，以 b 开头表示 Python 的 bytes 类型，可以使用 bytes 的 decode() 方法直接格式化，大大节省了工作量。
>
> ```python
> a = b'abc\nabc'
> print(a.decode())
> ```
>
> 

## 拍手叫绝

上面两个阶段已经耗费了好几天，这时看到攻防世界上已经有人提供了 Writeup，感到自己黔驴技穷，点看查看之后不禁拍手叫绝，这个办法是我绝对不可能想到的。

下面补充一下学到的知识。

Linux 内核提供了 /proc 文件系统，提供了在运行时访问内核内部数据结构、改变内核设置的机制，用户和应用程序可以通过 /proc 得到系统的信息，并改变内核的某些参数。/proc 文件系统是一个伪文件系统，它只存在内存当中，而不占用外存空间。/proc 目录下还包括 /proc/sys 目录，这个目录是可写的，可以通过它来访问或修改内核的参数。另外还有一些以数字命名的目录，它们是进程目录。系统中运行的每个进程都有一个对应的目录在 /proc 下，以进程的 PID号为目录名，它们是读取进程信息的接口。而 /proc/self 目录则是读取进程本身的信息接口。

读取 /proc/self/maps 可以得到当前进程的内存映射关系，通过读该文件的内容可以得到内存代码段基址。

/proc/self/mem 是进程的内存内容，通过修改该文件相当于直接修改当前进程的内存信息。

本题解答的关键思路是需要知道 Python 的存储对象的位置在堆上这个特性，而通过 /proc/self/mem 可以用来读取堆栈信息。

题目的代码和一键解决的文件，我已放在 [Github]() 上。

## 参考资料

1. [攻防世界 catcat-new](https://adworld.xctf.org.cn/challenges/details?hash=9cc7514c-7c47-11ed-ab28-000c29bc20bf&task_category_id=3)
2. [细说Jinja2之SSIT&bypass](https://baijiahao.baidu.com/s?id=1686667928214457971&wfr=spider&for=pc)
3. [Flask之Session伪造](https://blog.csdn.net/qq_45615127/article/details/117150993)
4. [客户端SESSION导致的安全问题](https://zhuanlan.zhihu.com/p/34936378)
5. [/proc/self](https://www.jianshu.com/p/3fba2e5b1e17)
6. [cat ctf wp](https://xia0ji233.pro/2023/01/01/Nepnep-CatCTF2022/#Cat-cat)
