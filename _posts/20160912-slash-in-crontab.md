---
title: Crontab中的除号（slash）到底怎么用？
date: 2016-09-12 16:57:01
tag: 
keywords: crontab, crontab 除号
description: crontab 是Linux中配置定时任务的工具，在各种配置中，我们经常会看到除号（Slash）的使用，那么这个除号到底标示什么意思，使用中有哪些需要注意的地方呢？
---

crontab 是Linux中配置定时任务的工具，如果对于 crontab 还不太熟悉的朋友，可以参考 [Crontab的格式](http://www.edulinks.cn/2009/04/22/20090422-crontab/) 。

在各种配置中，我们经常会看到除号（Slash）的使用，那么这个除号到底标示什么意思，使用中有哪些需要注意的地方呢？

在定时任务中，我们经常有这样的需求，比如每5分钟查看一下日志，我们可以这么写：

0,5,10,15,20,25,30,35,40,45,50,55 * * * * /command

这样肯定是没有问题的，但是就写书写起来非常复杂，简化一下可以这么写：

0-55/5 * * * * /command

这样的写法已经简化了很多，大家还是觉得麻烦，那么可以这样写：

*/5 * * * *　/command

怎么样，是不是非常简单明了。这句就表示在0，5,10,15,20,25,30,35,40,45,50,55分钟的时候执行命令，即每隔5分钟执行命令。

由此我们得出除号与范围搭配使用，表示命令执行的步长。

那么我下面这种写法，是否标示每13分钟执行一次命令呢？

*/13 * * * *　/command

这里就是需要注意的地方。有一些步长是无法用这种方式表达的。只有能够被整除的步长值才能表达与我们理解相一致确切的含义，对于分钟来说只有 */2,*/3,*/4,*/5,*/6,*/10,*/12,*/15,*/30，对于小时来说则只有*/2,*/3,*/4,*/6,*/8,*/12。对于日期来说，如果使用*/5，则不能保证每5天执行一次，到月底的时候可能只执行1天、2天或3天后就停止了，这取决与大小月与平闰年。这是因为 cron 是没有状态的，它不能够记录上次执行的是什么时间，只能根据当前的时间来判断是否需要执行。


而对于*/13这个例子来说，表示在分钟这个范围内，即0-59分钟，任务会每13分钟执行一次。即：0,13,26,39,52，之后再间隔8分钟，就会又重新执行。

再看下面一个例子：

0 6 */2 * * /command

看到这个我们如果理解成每xx执行就是，每两天的6点钟执行命令。在这里*的范围是1-31，*/2表示任务在奇数天执行，那么在1、3、5、7、8、10、12月，月末最后一天执行后，紧接着第二天仍然后执行，那这就不是每2天执行一次。

参考资料：

1、[What's the meaning of slash in crontab](http://unix.stackexchange.com/questions/32027/whats-the-meaning-of-the-slash-in-crontab)
2、Man 5 crontab
3、[Wiki Cron](https://en.wikipedia.org/wiki/Cron)
4、[StackOverflow Cron Syntax Query](http://stackoverflow.com/questions/6094999/crontab-syntax-query)
5、[What does the slash in crontab actually do？](https://binblog.info/2014/10/17/what-does-the-slash-in-crontab-actually-do/)