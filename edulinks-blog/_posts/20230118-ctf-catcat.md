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

提示有路径穿越漏洞，然后查看了一些系统的敏感文件，例如

* /etc/passwd
* 

后来在这个环节停顿了好几天，也曾尝试 SSTI 注入，发现不存在这个问题，这是后来获取到网站源码后才知道的。

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



## 拍手叫绝

上面两个阶段已经耗费了好几天，这时看到攻防世界上已经有人提供了 Writeup，感到自己黔驴技穷，点看查看之后不禁拍手叫绝，这个办法是我绝对不可能想到的。



## 参考资料

1. [攻防世界 catcat-new](https://adworld.xctf.org.cn/challenges/details?hash=9cc7514c-7c47-11ed-ab28-000c29bc20bf&task_category_id=3)
2. [细说Jinja2之SSIT&bypass](https://baijiahao.baidu.com/s?id=1686667928214457971&wfr=spider&for=pc)
3. [Flask之Session伪造](https://blog.csdn.net/qq_45615127/article/details/117150993)
4. [客户端SESSION导致的安全问题](https://zhuanlan.zhihu.com/p/34936378)
