---
title: 发布自定义的 Hexo 主题
date: 2022-09-20 19:02:58
keywords: Hexo, Hexo theme, Hexo 主题
description:
---

使用关键词 Hexo + 主题 能够搜索到很多如何配置使用 Hexo 主题的文章，也有一些介绍如何制作自己主题的文章，我也曾经写过一篇 [Hexo 主题开发准备工作](http://edulinks.cn/2020/12/23/20201223-develop-hexo-theme/)，但是很少有介绍如何将自己制作的主题发布到 Hexo 官网的中文文章。

[Hexo官网](https://hexo.io/docs/themes) 提供了一段简要的描述，本文在此基础上，以我自己开发的主题为例，详细介绍下如何将自己开发的 Hexo 主题发布到官方主题列表。

## 通过测试

官方文档建议主题发布之前需要通过单元测试，这个非常有必要，能帮我们发现一些开发过程中考虑不到的异常情况。测试的步骤是将 [hexo-theme-unit-test](https://github.com/hexojs/hexo-theme-unit-test) 工程下载到本地，然后安装配置需要测试的主题，根据检查列表检查各个测试文章是否能够正常显示。这是一个手工的测试过程，和我原来想象的自动化测试流程差别比较大。

我尝试了一个已经发布在官方主题列表页的主题 hexo-theme-yuzu，发现并不能通过测试，可以推断主题发布时对于测试是否通过并不是强制要求。

```sh
$ hexo s
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/ . Press Ctrl+C to stop.
ERROR Render HTML failed: index.html
TypeError: /Users/shiqiang/Projects/hexo-project/hexo-theme-unit-test/themes/hexo-theme-yuzu/layout/layout.ejs:3
    1| <!DOCTYPE html>
    2| <html lang="en">
 >> 3| <%- partial('_partial/head', null, {cache: false}) %>

```



 

## 参考资料

1. [Hexo themes](https://hexo.io/docs/themes)
