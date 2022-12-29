---
title: Leetcode:计算复数乘法
date: 2021-06-30 16:24:34
tags:
keywords: leetcode
description: Leetcode题目，计算两个复数的乘积。
---

### 题目要求

计算两个复数的乘积。

### 参考题解

这道题感觉很简单，主要是对复数的表达式进行解析，然后套用公式，输出结果就可以了。

```python
class Solution:
    def complexNumberMultiply(self, a, b):
        """
        :type a: str
        :type b: str
        :rtype: str
        """
        (x,y) = a.split("+")
        (m,n) = b.split("+")
        (y, k) = y.split("i")
        (n, k) = n.split("i")
        # print( (int)x*(int)m - (int)n*(int)y )
        return str( int(x)*int(m) - int(n)*int(y) ) + "+" + str( int(x)*int(n) + int(m)*int(y) ) + "i"
```


参考资料：
1、[题目网址](https://leetcode.com/problems/complex-number-multiplication/description/)
2、[Complex Number](https://en.wikipedia.org/wiki/Complex_number)