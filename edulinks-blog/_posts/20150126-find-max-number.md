---
title: 两个有序数组中查找第K大数
date: 2015-01-26 21:56:01
tag: 
keywords: 查找第K大数, 算法
description: 两个数组A、B，长度分别为m、n，即A(m)、B(n)，分别是递增数组。求第K大的数字。
---

题目：两个数组A、B，长度分别为m、n，即A(m)、B(n)，分别是递增数组。求第K大的数字。

方法一：
简单的办法，使用Merge Sort，首先将两个数组合并，然后在枚举查找。这个算法的时间复杂度是O（m＋n）、空间复杂度也是O（M＋n）。
这个方法其实没有考虑到有第K大数为两个相同数字的情况。

方法二：
这里需要两个前提条件，
1、如果K是中位数，则（M＋n）是奇数还是偶数是有关系的。如果是奇数，那么中位数唯一，如果是偶数就有两个中位数，可以随便取一个。
2、如果找到的第K大数是x，假如在A的位置是A(x)，在B中的位置是B(x)，则Ax+Bx-1=k是成立的。

接下来是具体实现逻辑：
1、首先假设K大数在A数组中，首先检查 (m/(m+n))*(k-1)，假设其值为A1。然后检查B中(k+1-(n/(m+n))*(k-1))假设为B1，检查A1、B1是否相等，或者大于B中的第(k+1-(n/(m+n))*(k-1))，并且小于(k+1-(n/(m+n))*(k-1))+1个元素。满足条件就可以知道A1就是所求，否则看条件2。
2、如果两个条件都不满足，那么需要判断第K个元素是位于A1左边还是右边。

如果A1>B1，那么K肯定不在A[0, (m/(m + n)) * (k - 1)]以及B[(k + 1 - (m/(m + n)) * (k - 1))+ 1, n]中；
如果A1<B1，那么K肯定不在A[ (m/(m + n)) * (k - 1), m]以及B[0, (k + 1 - (m/(m + n)) * (k - 1))]中。

第K个元素有可能在B中，同理可以假设在B中，再进行一次搜索。复杂度为log(m)+log(n)。

具体代码如下：
```c
int kthsmallest(int *a,int m,int *b,int n,int k) {
  if (m == 0) {
		return b[k - 1];
	}
  if (n == 0) {
    return a[k - 1];
  }
  if (k == 1) {
    return (a[0] < b[0])?a[0]:b[0];
  }
  if (k == m + n) {
    return (a[m - 1] > b[n - 1])?a[m - 1]:b[n - 1];
  }
  int i = ((double) m) / (m + n) * (k - 1);
  int j = k - 1 - i;
  if (j >= n) {
    j = n - 1;
    i = k - n;
  }
  if (((i == 0) || (a[i - 1] <= b[j])) && (b[j] <= a[i])) {
    return b[j];
  }
  if (((j == 0) || (b[j - 1] <= a[i])) && (a[i] <= b[j])) {
    return a[i];
  }
  if (a[i] <= b[j]) {
    return kthsmallest(a + i + 1, m - i - 1, b, j, k - i - 1);
  } else {
    return kthsmallest(a, i, b + j + 1, n - j - 1, k - j - 1);
  }

}
```

方法三：
当然也可以在方法一的基础上，采用一些二分的办法进行优化，但是这种算法

参考资料：
1、[http://www.cnblogs.com/davidluo/articles/k-smallest-element-of-two-sorted-array.html](http://www.cnblogs.com/davidluo/articles/k-smallest-element-of-two-sorted-array.html)
2、[http://blog.csdn.net/realxie/article/details/8078043](http://blog.csdn.net/realxie/article/details/8078043)
3、[http://blog.csdn.net/shifuwawa/article/details/6121573](http://blog.csdn.net/shifuwawa/article/details/6121573)
4、[http://eriol.iteye.com/blog/1172098](http://eriol.iteye.com/blog/1172098)