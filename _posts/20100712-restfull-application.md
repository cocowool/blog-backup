---
title: REST风格的应用程序实现
date: 2010-07-12 15:16:01
tag: 
keywords: REST, Restful, REST风格
description: REST是一种程序设计的风格，为我们整理自己的应用设计提供了一个原则，在利用这些原则带来的遍历的同时，可以根据实际情况进行灵活的处理。
---

莫笑我老土，因为我确实是最近才听说REST风格的，以前就是觉得 /category/product/pid
这样的地址非常的漂亮，但是那只是表象罢了，了解深入以后，发现必须有一个客户端的Ajax Engine和Server端的服务配合，才能实现一个REST风格的应用，下面就是我的实验。

**问题？**

要对外提供哪些服务。服务器端的服务可能会被众多的浏览器请求，也可能被第三方应用程序所调用，所以需要从总体上来考虑这个对外的“应用程序接口”（API），尽量保持接口的稳定性。REST是一种风格，并且形成了自己的规则，构建这样的应用，应尽量遵循REST的原则。

以一个足球服务为例，众多的观众会要求观看比赛的记录，上传新比赛记录，更新比赛记录，更正现有的比赛或者删除比赛等等。像这样描述的话，我们需要提供众多不同的服务，并且最终会倒在维护一致性的工作上。那么应该怎么做呢，考虑一下客户可能的请求方式：
> GET方式请求一个新建比赛服务：
[http://example.com/newMatch?id=995&bluebaggers=150&redlegs=60](http://example.com/newMatch?id=995&bluebaggers=150&redlegs=60)
POST或PUT方式请求一个新建比赛服务：
[http://example.com/newMatch](http://example.com/newMatch)
附加的XML为：
<match id="995">
<score team="bluebaggers">150</score>
<score team="redlegs">60</score>
</match>
CGI风格的POST或PUT请求：
[http://example.com/newMatch](http://example.com/newMatch)
请求体：
id=995&bluebaggers=150&redlegs=60
或者一个维护服务的GET请求：
[http://example.com/matchMaintenance/command=newMatch&id=995&bluebaggers=150&redlegs=60](http://example.com/matchMaintenance/command=newMatch&id=995&bluebaggers=150&redlegs=60)
或者POST请求
[http://example.com/matchMaintenance/command=newMatch](http://example.com/matchMaintenance/command=newMatch)
<match id="995"><score team="bluebaggers">150</score><score team="redlegs">60</score></match>

以此类推，可以有很多这样的功能。有些人觉得这并不是什么问题，对越来越多的请求，我们只要建立服务，然后给出相应的说明就可以了。但是，他还是存在缺点的。

也许我们会假设访问只是来自脚本，那么这种情况可能会简单一点。但实际上，还有很多的因素会涉及到，例如网页浏览器（会存在后撤和刷新按钮的问题）、Web服务器（可能会有缓存和编译问题）、网络路由和缓存问题、应对爬虫的骚扰、一些个人站点对网站内容的抓取。如果我们考虑这些不同的请求，我们的程序就可以表现的更健壮。

理想的情况下，一个服务应该有自我说明的能力。如果一个服务建立在一种约定俗成的条件下，那么大家就很容易适应并且进行后续的开发。

REST就是考虑了这些因素，可以使用RESTful API来实现上面的服务。

## **RESTful 原则介绍**
REST的主要原则有：
**用URL表示资源。**资源就像商业实体一样，是我们希望作为API实体呈现的一部分。通常是一个名词，每个资源都用一个独一无二的URL来表示。

**HTTP方法表示操作。**REST充分利用了HTTP的方法，特别是GET、POST、PUT和DELETE。注意XMLHttpRequest对象实现了全部的方法，具体可以参看[W3C HTTP 1.1 Specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。

也就是说，客户端的任何请求都包含一个URL和一个HTTP方法。回到上面的例子中，比赛显然是一个实体，那么对于一个特定比赛的请求就表示为：

> [http://example.com/matches/995](http://example.com/matches/995)

这种方式是清晰明了的，也许和精确命名的方式有所区别，但是只要遵循这种形式，我们就能很快的进行GET、DELETE、UPDATE和新建操作。

**RESTful的原则：**
1、URL表示资源
2、HTTP方法表示操作
3、GET只是用来请求操作，GET操作永远都不应该修改服务器的状态。但是这个也要具体情况进行分析，例如一个页面中的计数器，每次访问的时候确实引起了服务器数据的改变，但是在商业上来说，这并不是一个很重要的改变，所以仍然可以接收使用GET的方式来修改数据。

> 一个案例，使用GET方式修改数据遭受损失的案例
Witness the[the debacle](http://radar.oreilly.com/archives/2005/05/google_web_acce_1.html)caused by the Google Accelerator interacting with non-RESTful services in mid-2005. The accelerator jumps ahead of the user and prefetches each link in case they should click on it (a non-Ajaxian example of[Predictive Fetch](http://ajaxpatterns.org/Predictive_Fetch)). The problem came when users logged into non-RESTful applications like[Backpack](http://backpackit.com/). Because Backpack deletes items using GET calls, the accelerator - in its eagerness to activate each GET query - ended up deleting personal data. This could happen with regular search engine crawlers too, though the issue doesn't tend to come up because they don't have access to personal accounts.

4、服务应该是无状态的
在有状态的会话中，服务器可以记录之前的信息。而RESTful风格中是不应该让服务器记录状态的，只有这样服务器才具备可扩展性。当然，我们可以在客户端使用cookie，而且只能用在客户端向服务器发送请求的时候。

5、服务应当是“幂等”的
“幂等”表示可以发送消息给服务，然后可以再次毫不费力的发送同样的消息给服务。例如，发送一个“删除第995场比赛”的消息，可以发送一次，也可以连续发送十次，最后的结果都会保持一致。当然，RESTful的GET请求通常是幂等的，因为基本上不会改变服务器的状态。**注意**：POST请求不能被定义为“幂等”，特别是在创建新资源的时候，一次请求创建一个资源，多次请求会创建多个资源。

6、拥抱超链接

7、服务应当自我说明
例如[http://example.com/match/995](http://example.com/match/995)请求了一个具体的比赛，但是[http://example.com/match](http://example.com/match)并没有对任何实体进行请求，因此，应当返回一些介绍信息。

8、服务约束数据格式。数据必须符合要求的格式

在PHP的程序中，想要实现这种REST风格的URL，仅仅依靠程序是不行的，还需要在服务器端配置rewrite规则，例如，对于一个REST风格的资源请求：
[http://www.api.com/product/113](http://www.api.com/product/113)
一般实现的脚本为
[http://www.api.com/product.php?id=113](http://www.api.com/product.php?id=113)
这个是基于QueryString的，也可以做一个统一的 index.php 入口，然后通过处理URI的方式实现，例如：
[http://www.api.com/index.php/product/113](http://www.api.com/index.php/product/113)
这样的URL，都可以通过rewrite来实现rest风格。总之，REST是一种程序设计的风格，为我们整理自己的应用设计提供了一个原则，在利用这些原则带来的遍历的同时，可以根据实际情况进行灵活的处理。

参考资料：
1、[RESTful Service](http://ajaxpatterns.org/RESTful_Service)
2、[RPC Service](http://ajaxpatterns.org/RPC_Service)
3、[HTTP Methods](http://rest.blueoxen.net/cgi-bin/wiki.pl?HttpMethods)
4、[verbs Can also be nouns](http://rest.blueoxen.net/cgi-bin/wiki.pl?VerbsCanAlsoBeNouns)












