---
title: Python 网络请求模块 urllib 、requests
date: 2018-06-15 16:54
tags: 
---

Python 给人的印象是抓取网页非常方便，提供这种生产力的，主要依靠的就是 urllib、requests这两个模块。

### urlib 介绍
urllib.request 提供了一个 urlopen 函数，来实现获取页面。支持不同的协议、基本验证、cookie、代理等特性。
urllib 有两个版本 urllib 以及 urllib2。
urllib2 能够接受 Request 对象，urllib 则只能接受 url。
urllib 提供了 urlencode 函数来对GET请求的参数进行转码，urllib2 没有对应函数。
urllib 抛出了 一个 URLError 和一个 HTTPError 来处理客户端和服务端的异常情况。

### Requests 介绍
Requests 是一个简单易用的，用Python编写的HTTP库。这个库让我们能够用简单的参数就完成HTTP请求，而不必像 urllib 一样自己指定参数。同时能够自动将响应转码为Unicode，而且具有丰富的错误处理功能。

* International Domains and URLs
* Keep-Alive & Connection Pooling
* Sessions with Cookie Persistence
* Browser-style SSL Verification
* Basic/Digest Authentication
* Elegant Key/Value Cookies
* Automatic Decompression
* Unicode Response Bodies
* Multipart File Uploads
* Connection Timeouts
* .netrc support
* List item
* Python 2.6—3.4
* Thread-safe
> 以下为一些示例代码，本文环境为 Python 3.6.0


### 无需参数直接请求单个页面
```python
import urllib
from urllib.request import request
from urllib.urlopen import urlopen
# import urllib2
import requests

# 使用 urllib 方式获取
response = urllib.request.urlopen('http://www.baidu.com')
# read() 读取的是服务器的原始返回数据 decode() 后会进行转码
print(response.read().decode())

# 使用 requests 方式获取
# request 模块相比
resp = requests.get('http://www.baidu.com')
print(resp)
print(resp.text)
```
HTTP 是基于请求和响应的工作模式，urllib.request 提供了一个 Request 对象来代表请求，因此上面的代码也可以这么写
```python
req = urllib.request.Request('http://www.baidu.com')
with urllib.request.urlopen(req) as response:
print(response.read())
```
Request对象可以增加header信息
```python
req = urllib.request.Request('http://www.baidu.com')
req.add_header('User-Agent', 'Mozilla/6.0 (iPhone; CPU iPhone OS 8_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/8.0 Mobile/10A5376e Safari/8536.25')
with urllib.request.urlopen(req) as response:
print(response.read())
```
或者直接将 header 传入 Request 构建函数。

### 带参数的 GET 请求
带有参数的请求和上面的例子本质一样，可以事先拼出URL请求字符串，然后再进行请求。
本例使用了**腾讯**的股票API，可以传入不同的股票代码以及日期，查询对应股票在对应时间的价格、交易信息。
```python
# 使用带参数的接口访问
tencent_api = "http://qt.gtimg.cn/q=sh601939"

response = urllib.request.urlopen(tencent_api)
# read() 读取的是服务器的原始返回数据 decode() 后会进行转码
print(response.read())

resp = requests.get(tencent_api)
print(resp)
print(resp.text)
```

### 发送 POST 请求
urllib 没有单独区分 GET 和 POST 请求的函数，只是通过 Request 对象是否有 data 参数传入来判断。
```python
import urllib.parse
import urllib.request
url = 'http://www.someserver.com/cgi-bin/register.cgi'
values = {'name' : 'Michael Foord',
          'location' : 'Northampton',
          'language' : 'Python' }
data = urllib.parse.urlencode(values)
data = data.encode('ascii') # data should be bytes req = urllib.request.Request(url, data)
with urllib.request.urlopen(req) as response:
   the_page = response.read()
```
参考资料：
1、[python3 urllib.request 网络请求操作](https://www.cnblogs.com/cocoajin/p/3679821.html)
2、[Python3学习笔记（urllib模块的使用）](http://www.cnblogs.com/Lands-ljk/p/5447127.html)
3、[Python模拟登录的几种方法](https://www.cnblogs.com/chenxiaohan/p/7654667.html)
4、[What are the differences between the urllib, urllib2, and requests module?](https://stackoverflow.com/questions/2018026/what-are-the-differences-between-the-urllib-urllib2-and-requests-module)
5、[python3 urllib和requests模块](https://www.cnblogs.com/znyyy/p/7868511.html)












