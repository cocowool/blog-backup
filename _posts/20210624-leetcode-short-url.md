---
title: Leetcode:编码并解码短网址
date: 2021-06-24 08:26:59
tags:
keywords: 算法, leetcode, 刷题
description: 编写一个类，提供两个方法。一个可以将普通的网址编码成短网址，一个可以将短网址还原为普通网址。
---

### 题目要求

编写一个类，提供两个方法。一个可以将普通的网址编码成短网址，一个可以将短网址还原为普通网址。

### 参考题解

```python
# 使用随机函数，生成短网址，保存在dict中，避免重复
import random
import math
import re

class Codec:
    charbase = [x for x in "0123456789abcdefghijklmnopqrstuvwxyz"] 
    urldict = {}
    longurldict = {}

    def get_random_char(self, length):
        random_char = ""
        #print(len(self.charbase))

        for x in range(6):
            random_char += self.charbase[math.ceil(random.random() * len(self.charbase) ) - 1 ]

        return random_char

    def encode(self, longUrl):
        if longUrl in self.longurldict:
            return self.longurldict[longUrl]

        url_code = self.get_random_char(6)
        if url_code in self.urldict:
            self.encode(longUrl)
        else:
            self.longurldict[longUrl] = url_code
            self.urldict[url_code] = longUrl

        return "http://tinyurl.com/" + url_code

    def decode(self, shortUrl):
        domain = "http://tinyurl.com/"
        shortUrl = shortUrl.replace(domain, "")

        if shortUrl in self.urldict:
            return self.urldict[shortUrl]
        else:
            return false

url = "https://leetcode.com/problems/design-tinyurl";
codec = Codec()
print(codec.get_random_char(6))
print(codec.get_random_char(6))
print(codec.get_random_char(6))
print(codec.get_random_char(6))
print(codec.get_random_char(6))

print(codec.encode(url))
print(codec.decode(codec.encode(url)))


```

我这个过程遇到一个坑，本地环境是Python3，math.ceil函数返回了整型数，Leetcode是Python2的环境，所以返回了浮点数，需要做一下类型转换。看了一下其他解题方法，其实可以直接用 hash 函数，不用考虑那么多。