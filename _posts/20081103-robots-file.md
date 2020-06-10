---
title: Robots.txt和Robots META
date: 2008-11-03 09:40:09
updated: 2020-06-10 08:30:18
tag: 
---

**Robots Text File Robots.txt**

robots.txt是一套指示搜索引擎（蜘蛛）如何抓取你的网页内容的一张地图，他告诉蜘蛛哪些需要抓取，哪些不要抓取（当然，这只是针对那些遵守这个协定的搜索引擎）。robots.txt文件必须存放在网站的根目录下。
robots.txt中包括两部分主要内容：

```
User-agent:*
Disallow:
```


User-agent代表搜索引擎，可以指定特定的搜索引擎，星号表示所有；

Disallow，如果用不带/路径表示搜索引擎可以索引整个文件夹，如果带了/则表示对文件夹下的所有内容都不进行索引；
例如：/private/表示对于private文件夹下的所有内容不进行索引，/pulic表示索引所有public下的内容；

两种robots.txt的典型用法：

一、对所有搜索引擎完全开放：

```
User-agent：*
Disallow:
```

二、对所有搜索引擎关闭：

```
User-agent:*
Disallow:/
```

**Robots Meta Tag**

对于不能上传Robots.txt文件的用户，我们仍可以通过Robots Meta Tag来实现对于搜索引擎的屏蔽。
<meta name="robots" content="robots-term" />

robots-term是一系列逗号分隔的字符，主要有以下几个值：noindex,nofollow,index,follow,all
下面来简单解释一下各自的含义：

* noindex:搜索引擎不会为页面建立索引，但是允许页面上的链接被追踪；
* nofollow:搜索引擎不会追踪页面中的链接，但是允许搜索引擎收录该页面；
* index：搜索引擎将把该页面加入到搜索服务中；
* follow：搜索引擎会从该页面去查找其他的页面；
* all：搜索引擎会执行上面两种操作；

如果Meta标签缺失，或者robots-term没有指定明确的值，那么默认是index,follow，如果在meta中指定了all，那么其他的所有值都将被覆盖，不管all出现在什么位置

最后，再贴一个比较规范的robots.txt来，以供学习：

robots.txt for http://www.SEOConsultants.com/

Last modified: 2008-10-19T02:30:00-0700

```
User-agent: googlebot
Disallow: /js/
Disallow: /webservices/

User-agent: slurp
Disallow: /js/
Disallow: /webservices/

User-agent: msnbot
Disallow: /js/
Disallow: /webservices/

User-agent: teoma
Disallow: /js/
Disallow: /webservices/

User-agent: Mediapartners-Google*
Disallow:

User-agent: *
Disallow: /
```