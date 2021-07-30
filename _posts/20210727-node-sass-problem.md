---
title: 20210727-node-sass-problem
date: 2021-07-27 13:32:34
tags:
---

前两天系统安装了 `nmap` 后，`hexo new` 新文章的时候就报下面的错误。

```sh
INFO  Validating config
ERROR {
  err: Error: Node Sass does not yet support your current environment: OS X 64-bit with Unsupported runtime (93)
  For more information on which environments are supported please see:
  https://github.com/sass/node-sass/releases/tag/v4.14.1
      at module.exports (/Users/shiqiang/Projects/edulinks-blog/node_modules/node-sass/lib/binding.js:13:13)
      at Object.<anonymous> (/Users/shiqiang/Projects/edulinks-blog/node_modules/node-sass/lib/index.js:14:35)
      at Module._compile (node:internal/modules/cjs/loader:1095:14)
      at Object.Module._extensions..js (node:internal/modules/cjs/loader:1124:10)
      at Module.load (node:internal/modules/cjs/loader:975:32)
      at Function.Module._load (node:internal/modules/cjs/loader:816:12)
      at Module.require (node:internal/modules/cjs/loader:999:19)
      at req (/Users/shiqiang/Projects/edulinks-blog/node_modules/hexo/lib/hexo/index.js:292:23)
      at /Users/shiqiang/Projects/edulinks-blog/node_modules/hexo-render-sass/dist/index.js:10:35
      at /Users/shiqiang/Projects/edulinks-blog/node_modules/hexo/lib/hexo/index.js:305:14
      at tryCatcher (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/util.js:16:23)
      at Promise._settlePromiseFromHandler (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/promise.js:547:31)
      at Promise._settlePromise (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/promise.js:604:18)
      at Promise._settlePromise0 (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/promise.js:649:10)
      at Promise._settlePromises (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/promise.js:729:18)
      at _drainQueueStep (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/async.js:93:12)
      at _drainQueue (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/async.js:86:9)
      at Async._drainQueues (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/async.js:102:5)
      at Immediate.Async.drainQueues [as _onImmediate] (/Users/shiqiang/Projects/edulinks-blog/node_modules/bluebird/js/release/async.js:15:14)
      at processImmediate (node:internal/timers:464:21)
} Plugin load failed: %s hexo-render-sass
```

看了一下本机 node 版本已经升级到了 v16.5.0。

```sh
$ node --version              
v16.5.0
```

查看 node-sass 的版本是4.14.1，从 node-sass 的官方看到这个版本是不支持 node v16版本的，要从 node-sass 6.0.0 之后才开始支持。尝试安装最新版本的 node-sass 报错依旧，探索了几天觉得应该是 node-gyp 环境问题造成的。

## node-gyp

[gyp](https://gyp.gsrc.io/index.md) 是一个用来生成项目文件的工具，一开始是设计给 chromium 项目用的，生成项目文件后可以调用GCC、vsbuild、xcode等不通平台的编译工具进行编译。node 程序中需要调用一些其他语言编写的工具或dll，因此首先需要编译一下，否则会出现跨平台后无法正常使用的问题。

## 因祸得福

本次熟悉了几个命令

* `npm config set/get` 设置npm的配置或读取npm的配置
* `nom config ls -l` 查看npm的详细配置
* `nom ls -g` 查看npm安装的全局包
* `npm outdated -g` 查看需要更新的全局包，不加参数表示查看本地需要更新的包
* `npm uninstall -g hexo` 卸载全局安装的包
* `npm update -g hexo` 更新全局安装的包 

## 参考资料

1. [node-gyp的作用是什么](https://www.zhihu.com/question/36291768)
2. [macOS 下 node-gyp rebuild failed 的解决方法](https://www.jianshu.com/p/c16c4b6212b9)
3. [Apple Developer Downloads](https://developer.apple.com/download/all/?q=xcode)
4. [Installation notes for macOS Catalina](https://github.com/nodejs/node-gyp/blob/master/macOS_Catalina.md)
5. [GIthub node-gyp](https://github.com/nodejs/node-gyp#readme)

