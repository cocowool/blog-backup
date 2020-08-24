---
title: PHP中如何保持SESSION以及由此引发的一些思考
date: 2009-08-01 23:40:01
tag: 
---


最近的一个项目，里面有一个比较大的表单，用户完成它需要很多时间，很多用户花了千辛万苦完成之后，一提交发现SESSION过期，系统退出了，所以引起了研究如何设置SESSION以及保持SESSION在线的需要，下面是一些心得体会。

什么是SESSION？

按照WIKI的解释，SESSION是存在于两个通信设备间的交互信息，在某一时间建立，经过一定的时间后失效。常见的SESSION有：TCP SESSION、WEB SESSION（HTTP SESSION）、LOGIN SESSION等。
根据OSI模型中，会话实现的位置不同，SESSION主要分为几种，一种是应用层会话，包括WEB SESSION（HTTP　SESSION）和telnet远程登录session；会话层实现的，包括Session Initiation Protocol（SIP）和Internet Phone Call；在传输层实现的有TCP　SESSION。
本文主要讨论WEB SESSION，其一般有两种：客户端SESSION和服务器端SESSION，后一种最常见的属于Java Beans提供的。

SESSION是做什么的？

在计算机领域，特别是网络方面，SESSION使用的特别广泛，也可以称为是对话（Dialogue）、会话等，一般是指在两个通信设备间存储的状态，有时也发生在用户和计算机之间（Login　SESSION）。
区别于无状态的通信，SESSION通常用来存储通信状态，因此通信的双方至少有一方需要存储SESSION的历史记录，从而实现两者间的通信。

SESSION（WEB　SESSION）是怎么实现的？

浏览器和服务器之间进行HTTP通信时，通常会包含一个　HTTP Cookie 来标识状态，通常会有一个唯一的　SESSIONID　，SESSION通常记录着用户的一些验证信息和级别。
在几中编程语言中最常用的Http Session Token是，JSESSIONID（JSP），PHPSESSID（PHP），ASPSESSIONID（ASP），这个标识通常由哈希函数产生，能够唯一表示这个用户的身份，在服务器和客户端通信时，作为GET或者POST的参数存储在客户端。
SESSION的实现方式通常有两种，服务器端SESSION和客户端SESSION，两种方式各有优缺点。

服务器端SESSION实现容易并且效率比较高，但是遇到负载均衡或者高可用性需求的时候，处理起来就比较困难，对于那种内生系统不存在存储设备的时候，也是不可用的。负载均衡可以通过共享文件系统或者强制客户只能登录到一台服务器上来实现，但是这样会降低效率。对于没有存储的设备，也可以通过使用RAM（参考参考资料6）来解决服务器端SESSION的实现，这种方法这对哪些客户端链接有限的系统有效（诸如路由或者接入点设备）。
客户端SESSION的使用可以解决服务器端SESSION的一些问题，比如避免了负载均衡的算法等，但是同时也会产生一些自身的问题。客户端SESSION使用Cookie和加密技术来在不同的请求间保存状态。在每一个动态页面结束后，会统计当前的SESSION，并把它发回客户端。每次成功请求后，会把cookie再发送到服务器端，来让服务器“记起”这个用户的身份。客户端SESSION最重要的问题就是安全问题，一旦cookie被劫持或者篡改了，用户的信息的安全性就丧失了。

PHP中如何设置SESSION？

搭建好PHP的开发环境后，通过phpinfo()可以查看到与SESSION有关的部分包括：
SESSION模块，在PHP V5.2.9版本中，一共有25个变量。其中，平时设置中常会用到的几个有：
session.cookie_lifetime        设置存储SESSIONID的cookie过期时间
session.name                SESSION的COOKIE名称，默认为PHPSESSID
session.save_handler        SESSION的存储方式，默认为FILE
session.save_path            Fedora下面默认存储在/var/lib/php/session

session.gc_probability
session.gc_divisor
session.gc_maxlifetime        这三个选项用来处理GC机制发生的机率

session.cache_limiter    （nocache,private,private_no_expire,public）
session.cache_expire    这两个选项是用来缓存SESSION的页面

先来考虑第一个问题，SESSION多久会过期，他是如何过期的？如果要在PHP程序中使用SESSION，一定要先引用session_start()，这个函数一执行，就会在SESSION的存储目录（如果使用了file handler)生成一个SESSION文件，里面内容是空的，同时浏览器会见里一个name为PHPSESSID的cookie，里面存储着一个hash出来的SESSION的名字。
SESSION的过期依赖于一个垃圾回收机制（Garbage Collection），SESSION创建后作为一个文件存放在服务器上，客户端脚本每访问一次SESSION中的变量，SESSION文件的访问时间就会进行更新。每次访问都是根据客户端存储的SESSIONID去请求服务器中存储的唯一的SESSION，当客户端的cookie过期后，就无法知道要访问的是哪一个SESSION，尽管此时服务器上的SESSION文件还没有被过期收回，这样就会造成服务器资源的浪费。
但是同时，如果我们希望用户的session马上过期的话，我们就可以通过设置cookie的办法来实现。SESSION的回收是在每次访问页面的时候进行的，回收的机率由session.gc_probability，session_gc_divisor指定，默认士1/100。如果设置为1，则每次超过了SESSION的生存周期去访问的话，SESSION一定会被回收。

两种需求：1、保持SESSION不过期或延长SESSION过期时间；2、使SESSION立即过期。
1、保持SESSION不过期和延长SESSION过期时间非常必要，特别是在内部应用系统中或者有很大的表单的时候。想想你的老板在填写一个表单，刚好碰上午饭时间，留着这个表单等吃饭回来，填写完剩余的内容，提交后他看到什么，一般来说都是一个登录界面。想要提高用户体验，关键是要让老板的表单不出问题，我们就必须延长SESSION的生存周期。
保持SESSION不过期和延长SESSION过期时间，可以通过设置session.gc_maxlifetime来实现，不过首先需要保证客户端的cookie不会在gc执行回收之前失效。通过设置一个较长的gc_maxlifetime可以实现延长session的生存周期，可是对于不是所有请求都会保持很久的应用来说，这么做对于服务器配置显然不是一个最佳的选择。
我们知道SESSION的回收机制是根据SESSION文件的最后访问时间来判断的，如果超过了maxlifetime，则根据回收机率进行回收。所以我们只需要定期的去访问一下SESSION就可以了，而这可以通过刷新页面来实现，根据这个思路，解决的方法就有了。
通过JS定期的去访问页面；
利用Iframe定期的刷新页面；
直接利用程序发送HTTP请求，这样就可以避免在页面中嵌入其他的元素；

下面是利用JS发送请求实现的保持SESSION不过期的实现方法，这样我们就只需要在需要SESSION保持长时间的页面（比如大表单页面）。
<script type="text/javascript">
function keepMeAlive(imgName){
myImg = document.getElementById(imgName);
if(myImg) myImg.src = myImg.src.replace(/\?.*$/, '?' + Math.random());
}

window.setInterval("keepMeAlive('phpImg');", 4000);
</script>
<img id="phpImg" src="http://www.phpplot.com/phpplot/session/sess_refresh.php?" width="1" height="1" />
其中URL后加入一个随机数是为了避免这个链接的请求被浏览器缓存。

2、使SESSION立即过期的方法就比较多了，我们可以session_destroy()，也可以用上面的思路，请求一个session_destroy的页面。

SESSION安全吗？

PHP的手册中明确写出：SESSION并不能保证储存在SESSION中的信息一定只能被他的创建者所看到。
如果想要安全的处理一些远程的操作，那么HTTPS是唯一的选择。最基本的，不要认为一个用户信息在SESSION中存在就认为这个用户一定就是他本人，虽然SESSION中的信息会给你他已经经过了用户名和密码验证的假象。所以，如果需要做一些修改密码或者类似的事情的时候，让用户重新输入密码是一个比较好的选择。
早期的Apache版本并没有采用COOKIE的方式来存储PHPSESSID，而是采用的URL-rewrite，也就是每个URL后面都会加上PHPSESSID=<sessionid>来表明它属于那个激活的SESSION，新版的Apache已经将这个属性设置为默认关闭。
session.use_trans_id = 0;

所以从这个意义上来讲，延长SESSION的时间过长或者保持SESSION一直在线对于安全来说始终不是一件好事情。终极的解决办法就是用户提交跳转到登录窗口，登录后又能够回到填写页面，并且所有的数据都还在。这个的实现方式现在用Ajax来解决应该没什么困难，每隔一定时间就把当前的用户数据POST到一个存储位置，不管是XML或者JSON。

拾遗：

对于客户端不支持JavaScript的情况可以采用的方法：
1、写一个浮层，显示在最顶层，如果用户未禁用JS，则让浮层消失；
2、将所有的INPUT都设置为disable，然后再用JS设置为enabled；
以上这两种方式都是在JS被禁用的时候，所有功能都不能用，如何在JS被禁用的情况下使我们的应用仍然正常工作，这个貌似就比较困难。实现这个的所花的时间和所收到的效果大家要权衡一下。

### 参考资料
1、SESSION Security                http://uk2.php.net/manual/en/session.security.php
2、WIKI    SESSION                    http://en.wikipedia.org/wiki/Session
3、SESSION（computer science）    http://en.wikipedia.org/wiki/Session_(computer_science)
4、Http Cookie                    http://en.wikipedia.org/wiki/HTTP_cookie
5、PHP.net SESSION                http://cn.php.net/manual/en/intro.session.php
6、OSSP mm                        http://www.ossp.org/pkg/lib/mm/











