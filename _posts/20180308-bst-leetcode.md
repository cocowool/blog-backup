---
title: Binary Search Tree 以及一道 LeetCode 题目
date: 2018-03-08 11:08:11
tag: 
category: 开发
keywords: leetcode, leetcod 刷题
description: Binary Search Tree 以及一道 LeetCode 题目
---

### 一道LeetCode题目

今天刷一道LeetCode的题目，要求是这样的：
> Given a binary search tree and the lowest and highest boundaries as```L```and```R```, trim the tree so that all its elements lies in [```L```,```R```] (R >= L). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.

由于对于 Binary search tree 不理解，所以绕了点弯路，要解这道题，必须理解什么是 binary search tree。我们来看一下定义：
> A binary search tree is a rooted binary tree, whose internal nodes each store a key (and optionally, an associated value) and each have two distinguished sub-trees, commonly denoted left and right. The tree additionally satisfies the binary search property, which states that the key in each node must be greater than or equal to any key stored in the left sub-tree, and less than or equal to any key stored in the right sub-tree.[1]:287 (The leaves (final nodes) of the tree contain no key and have no structure to distinguish them from one another.

看一下下面这个图一下子就能理解，就是说每个节点左边的值一定小于右边。
![](20180308-bst-leetcode/39469-20180308133853584-434040861.png)
了解到这个约束，这个题目解起来就比较简单了：

```python
class Solution:
    def trimBST(self, root, L, R):
        """
        :type root: TreeNode
        :type L: int
        :type R: int
        :rtype: TreeNode
        """
        if(root.val < L):
            if(root.right != None):
                root = self.trimBST(root.right, L, R)
            else:
                return None
        elif(root.val > R):
            if(root.left != None):
                root = self.trimBST(root.left, L, R)
            else:
                return None
        else:
            if(root.left != None):
                root.left = self.trimBST(root.left, L, R)
            else:
                root.left = None
                
            if(root.right != None):   
                root.right = self.trimBST(root.right, L, R)
            else:
                root.right = None
                
        return root
```

### 参考资料：
1、[Leetcode](https://leetcode.com/problems/trim-a-binary-search-tree/)
2、[Wiki Binary search tree](https://en.wikipedia.org/wiki/Binary_search_tree)












