---
title: Ajax与REST
date: 2010-07-12 11:04:01
tag: 
keywords: ajax, rest, restful
description: REST(Representational State Transfer)是一种开发思想。
---

REST(Representational State Transfer)是一种开发思想，互联网不断发展，富媒体越来越多的应用，不断地冲击着我们的应用，使应用的可扩展性和系统复杂性不断增加，还好，出现了基于Ajax的应用架构，让我们可以继续REST的风格。

尽管互联网在几十年前就在实验的环境下产生了，但是真正的扩张还是在 Tim Berners-Lee完成了网络几个主要组件的原型之后，它们包括：URI,HTTP,HTML 浏览器和服务器。但是快速的发展超出了人们的预期，带来了隐忧：

> "Although elated by its success, the Internet developer community became concerned that the rapid growth in the Web's usage, along with some poor network characteristics of early HTTP, would quickly outpace the capacity of the Internet infrastructure and lead to a general collapse."          --- Roy Fielding

Fielding 和其他人一起重新检验了网络的架构和应对大量应用的不足。总结出了明确的改进标准，比如URI和HTTP的建议，同时也模糊的提出了一套为互联网程序使用的新架构，他总结为 Representational State Transfer(REST)。他指出，遵循这个标准，我们可以享受互联网提供的属性，不遵守，则会很难过。

**网络应用的历史介绍**
Berners-Lee最早是为了研究者之间能够共享文档而创建了互联网，但是这种URI的标准结构特点却迅速的被用来共享更多的静态文档以外的内容。

**静态文件网站**
最早的网站内容都是一些静态的HTML文件，并且互相之间存在着链接关系，可以用下图表示。
![](20100712-ajax-and-rest/image_thumb.png)

使用REST风格，基于URI和最后修改时间（Last-modified）状态可以很高效并且可扩展的存取静态文件内容。但是仅仅有静态内容是不够的。

**早期的动态网站应用**
Berners-Lee和其他人设计了URI标准来表示唯一的资源定位，而且允许自愿的表现(Representation, e.g HTML\text\etc)根据浏览器和服务器之间的通信而发生变化。因为URI定位符区分了资源标识和底层储存机制，网络开发人员就能够创建程序来检查URI的语法，并且动态的生成一些文档。通常是整合一些预定义的UI和从数据库中获得的一些数据。

![](20100712-ajax-and-rest/image_thumb_1.png)

以大学里目录应用程序举个例子，典型的流程大概是这样：
1、用户输入姓名并提交（例如是：Bill Higgins）
2、表单在输入的基础上产生一个对服务器的URI请求，例如GET：[http://psu.edu/Directory/Bill+Higgins](http://psu.edu/Directory/Bill+Higgins)
3、服务器检查URI，并且生成包含学生信息的页面
4、最后服务器将这些信息返回给用户

**拟真的网络应用**
下一代互联网应用的目标是提供高度的你拟真、个人定制内容以及丰富的应用模型。

**服务器端拟真和REST**
REST的“无状态服务器”不允许在服务器上保存SESSION信息，这样可以保证服务器具有可靠和可扩展的特性，但是目前的应用需要提供一个可以定制化的应用，那么只有两种选择。一种办法是随着每个请求，附带着相关的状态信息，这样服务器可以确保每个请求是“有状态”的。另外一种方法是在服务器上保存用户的SESSION对象，每次请求时附加与SESSION匹配的token。后一种方法虽然支持了用户函数以及用户的定制，但却打破了“无状态服务器”的约束，破坏了原先设计的架构。

![](20100712-ajax-and-rest/image_thumb_2.png)

Java的Servlet HttpSession API提供了相关的例子。事实上，SESSION中可以存储任何对象，存储的东西越来越多后，你会发现服务器占用的内存和资源越来越多，最后不得不使用集群来满足对于资源的需求。使用集群后，就需要考虑如何在不同的服务期间传输用户的状态数据。当其中一台挂掉时，是否保留它的状态信息并且在重启后恢复。那么，维护这些状态是否有用呢。

分布式Cache无法实现
另外一个服务器端保存状态的缺点无法完全利用REST的第一个有点 - 缓存。Fielding说过“缓存的一个优点是部分或全部的减少互动，提高效率和可扩展性，提高客户端的效率。负面影响是，已经被显著修改过的数据，仍从缓存中发送出去将会降低可信度。”。

针对这种无法缓存的情况也有一些解决办法。一种就是细分资源的粒度，将页面中部分不常发生变动的资源缓存起来，服务器将各个部分组合起来发送给客户端。但是无法根本性的解决问题，每个请求中都需要服务器的处理，因此可扩展性和用户响应时间都受到了影响。

动态网站应用不能使用缓存的另外一个缺点是需要禁止搜索引擎发起的请求，因为每个请求都会消耗很多的资源。而在一个REST风格的应用中，每次搜索引擎请求的时候，你可以简单的返回一个“Not-Modified”信息。

**Ajax 和 REST**
传统的应用程序在服务器端整合表现和动态内容，然后再发送给用户。Ajax应用程序有些不多，它的主要界面和应用逻辑都在客户端，在必要的时候客户端从服务器取回新的数据，并且显示在当前的页面中。展示层的位置和数据绑定看起来太细节了，但是正是这些不同产生了不同的架构风格。
享受有状态的Web客户端。

人们常常用多次点击而不用刷新页面来描述Ajax应用，描述是准确的，而且言下之意表明用户并不喜欢全页面的刷新。从架构设计的角度来讲，全页面刷新消除了在客户端保存状态数据的可能，最后导致了完全不同的架构设计。

事实上Ajax允许我们不用刷新整个页面就可以与服务器互动，这预示了实现动态网络应用架构的可能，因为程序资源和数据资源的绑定转移到了客户端，服务器端就可以很好的处理动态和定制的问题，现在能够同时保证架构简单、可扩展以及RESTful的应用实现。

**缓存Ajax引擎**
使用Ajax引擎的应用，能够将很多的应用逻辑（使用Javascript实现）和一些页面元素，这样就减轻了服务器的请求和处理压力。

![](20100712-ajax-and-rest/image_thumb_3.png)

虽然Ajax引擎包含了很多应用逻辑和其他元素，但是如果设计得当，可以实现不包含任何商业数据或个性化的内容，这样就为其扩展性提供了较大的可能。所以在进行应用程序设计时，将REST的原则应用到Ajax引擎中，可以为我们访问应用程序资源提供更大的便利。

Ajax的一些数据，例如一些公共数据，一本书的详细信息，一个商品的介绍页面，这些信息完全可以被缓存起来。

> * ```/Books/0201633612```(where```0201633612```is the design pattern book's ISBN number)
> * ```/PurchaseHistory/0201633612/bhiggins@us.ibm.com```


Ajax的应用，同时在一定程度上增强了程序的鲁棒性。因为很多数据都存储在客户端，即便是某一时刻，服务器Down了，用户仍能够对现有的数据进行浏览。

[总结]
IBM DW里的这篇文章虽然没有多少实际的技术指导，但是从历史上介绍REST开始入口，到逐步的引入Ajax Engine，让我明白了整个架构的演变过程。同时也对于REST开始有了初步的认识，但是REST风格的架构如何设计，如何在目前自己的应用中使用REST风格，还是一个需要摸索的过程。今天看到Puppet（一个服务器配置管理工具）也在进行REST风格的重写，让我感觉的，这种架构应当是一个很好的管理自己应用风格的方式。


参考资料：
1、[Ajax and REST](http://www.ibm.com/developerworks/xml/library/wa-ajaxarch/)
2、[Ajax and REST 中文](https://www.ibm.com/developerworks/cn/web/wa-ajaxarch/)
3、[Ajax and REST 第二部分 中文](http://www.ibm.com/developerworks/cn/web/wa-ajaxarch2.html)
4、[REST](http://zh.wikipedia.org/zh-cn/REST)












