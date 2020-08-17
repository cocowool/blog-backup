---
title: 计算质数和回文数的小程序
date: 2012-05-18 15:24:01
tag: 
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
具体代码如下：
1:def isZhishu?(num, arrZhishu)
2:returntrueifnum == 1 || num == 2
3:count = 2
4:
5:if( arrZhishu.empty? )then
6:#count = 2
7:whilecount < numdo
8:returnfalseif( num % count ) == 0
9:if( count >= 11 ) then
10:count = count + 2 # Only judge even numbers
11:else
12:count = count + 1
13:end
14:end
15:
16:returntrue
17:else
18:arrZhishu.each{|value|
19:nextifvalue== 1
20:returnfalseif( num %value) == 0
21:}
22:returntrue
23:end
24:end
25:
26:def isHuiwen?(num)
27:s = num.to_s.reverse.to_i
28:returntrueifnum == s
29:
30:returnfalse
31:end
32:
33:count = 0
34:arrZhishu = Array.new
35:i = 0
36:whilei < 10000000do
37:ifi >= 11 then
38:i = i + 2
39:else
40:i = i + 1
41:end
42:
43:ifisZhishu?(i, arrZhishu) && isHuiwen?(i) then
44:arrZhishu.push(i)
45:#printf"%4d", i
46:#print"\n"
47:count = count + 1
48:end
49:end
50:print"\n\nTotal:#{count}"













