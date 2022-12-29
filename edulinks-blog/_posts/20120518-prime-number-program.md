---
title: 计算质数和回文数的小程序
date: 2012-05-18 15:24:01
tag: 
keywords: ruby, ruby计算质数, ruby回文数
description: ruby实现的计算质数和回文数的两个小函数。
---

Ruby 代码如下

```ruby
def isZhishu?(num)
	count = 2
	whilecount < numdo
		returnfalseif( num % count ) == 0
		count = count + 1
	end

	return true
end

def isHuiwen?(num)
	s = num.to_s.reverse.to_i
	return true ifnum == s

	return false
end

count = 0
10000.times{ |i|
	i = i + 1
	ifisZhishu?(i) && isHuiwen?(i) then
		printf"%4d", i
		print"\n"
		count = count + 1
	end
}

print"\n\nTotal:#{count}"
```

上面这个方法非常笨重，时间复杂度是 O（n^2），可以进行一些优化。根据[@](http://www.cnblogs.com/cocowool/#2383293)sdpfoue 的建议，做了优化。
首先就是可以只对大于3的奇数进行检查，因为偶数肯定可以被2整除，所以不需要考虑。

另外循环相除的时候，可以只除以质数，这样也能够减少不少步骤。但是会增加空间的消耗，就是所谓的用空间换时间。