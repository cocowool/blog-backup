---
title: Leetcode:构建最大数二叉树
date: 2021-06-28 10:33:20
tags:
keywords: 算法, leetcode, 刷题
description: 构建最大数二叉树。
---

### 题目要求，[题目地址](https://leetcode.com/problems/maximum-binary-tree/description/)

给定一个不含重复数字的数组，最大二叉树构建规则如下：
1、根是数组中最大的数字
2、左边的子树是最大数字左边的内容
3、右边的子树是最大数字右边的内容

### 答案

```python
class Solution(object):
    def constructMaximumBinaryTree(self, nums):
        """
        :type nums: List[int]
        :rtype: TreeNode
        """
        #print(max(nums))
        #print(nums.index(max(nums)))
        #print(nums)
        if len(nums) == 0:
            return None
        
        t = TreeNode(max(nums))
        if len(nums) > 1:
            t.left = self.constructMaximumBinaryTree(nums[:nums.index(max(nums))])
            t.right = self.constructMaximumBinaryTree(nums[nums.index(max(nums)) + 1 :])
        else:
            t.left = None
            t.right = None
        
        return t
```

### 优化思路

提交后这个答案只打败了17%的对手，分析一下感觉是因为index、len函数调用次数过多，应该可以将结果放在一个变量中，提高执行效率。