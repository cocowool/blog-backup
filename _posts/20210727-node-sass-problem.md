---
title: node-sass安装遇到的错误
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

为了能够

1. 初始化一个Hexo博客，尝试安装 hexo-renderer-sass看是否报错。搞明白npm install hexo-renderer-sass 的过程，看是哪个环境依赖导致的问题。
2. 初始化一个npm项目，安装并调用node-sass看是否报错。
3. 创建一个新的hexo插件，支持sass的编译。



## node-gyp

[gyp](https://gyp.gsrc.io/index.md) 是一个用来生成项目文件的工具，一开始是设计给 chromium 项目用的，生成项目文件后可以调用GCC、vsbuild、xcode等不通平台的编译工具进行编译。node 程序中需要调用一些其他语言编写的工具或dll，因此首先需要编译一下，否则会出现跨平台后无法正常使用的问题。

## 因祸得福

本次熟悉了几个命令

* `npm config set/get` 设置npm的配置或读取npm的配置
* `npm config get cache` 查看npm缓存目录的位置
* `npm config get prefix` 查看全局npm安装的路径，查到之后可以在 `${prefixUrl}/lib/node_modules/npm` 下看到npm的源码，例如想要查看 `npm install` 的代码，可以在 `/usr/local/lib/node_modules/npm/lib/install.js` 中看到
* `npm config ls -l` 查看npm的详细配置
* `npm ls -g` 查看npm安装的全局包
* `npm outdated -g` 查看需要更新的全局包，不加参数表示查看本地需要更新的包
* `npm uninstall -g hexo` 卸载全局安装的包
* `npm update -g hexo` 更新全局安装的包 
* `npm install /local/plugin/path` 可以本地安装npm包
* 

## 问题复现

```sh
$ hexo init test-blog
...
INFO  Start blogging with Hexo!
```

看到成功信息后，使用 `hexo server` 检查能否正常打开页面。



## 另外一个问题

为了更深入了解 node-sass 写了一个js脚本用来测试node-sass的使用，脚本内容很简单，仅仅引用了node-sass模块，执行的时候报错，提示找不到 `vendor` 目录。

```sh
$ node test.js                        
node:internal/fs/utils:343
    throw err;
    ^

Error: ENOENT: no such file or directory, scandir '/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/vendor'
    at Object.readdirSync (node:fs:1380:3)
    at Object.getInstalledBinaries (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/extensions.js:134:13)
    at foundBinariesList (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/errors.js:20:15)
    at foundBinaries (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/errors.js:15:5)
    at Object.module.exports.missingBinary (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/errors.js:45:5)
    at module.exports (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/binding.js:15:30)
    at Object.<anonymous> (/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/lib/index.js:13:35)
    at Module._compile (node:internal/modules/cjs/loader:1095:14)
    at Object.Module._extensions..js (node:internal/modules/cjs/loader:1124:10)
    at Module.load (node:internal/modules/cjs/loader:975:32) {
  errno: -2,
  syscall: 'scandir',
  code: 'ENOENT',
  path: '/Users/shiqiang/Projects/www.edulinks.cn/test-blog/node_modules/node-sass/vendor'
}
```

进入到 `node_modules/node-sass` 下面看确实没有 vendor 目录，

按照官方的指引执行下面的命令

```sh
$ cd node-sass
$ npm install
$ node scripts/build -f  # use -d switch for debug release
# if succeeded, it will generate and move
# the binary in vendor directory.
```

## Sass

在今年2月份的时候，还写了一篇介绍SASS的文章《[SASS学习笔记](http://www.edulinks.cn/2021/02/08/20210208-sass-notes/)》，这篇中仅记录了语法方面的知识，没有介绍安装使用方面的内容。

翻阅了sass的官方网站，发现sass安装有两种模式，一种是本地命令的方式，在Mac中可以通过`brew install sass` 进行安装，安装完成后执行下面命令可以确认安装是否成功。

```sh
$ sass --version
1.35.2 compiled with dart2js 2.13.4
```

也可以使用 `npm install -g sass` 命令来安装JS版本的sass。






## 参考资料

1. [node-gyp的作用是什么](https://www.zhihu.com/question/36291768)
2. [macOS 下 node-gyp rebuild failed 的解决方法](https://www.jianshu.com/p/c16c4b6212b9)
3. [Apple Developer Downloads](https://developer.apple.com/download/all/?q=xcode)
4. [Installation notes for macOS Catalina](https://github.com/nodejs/node-gyp/blob/master/macOS_Catalina.md)
5. [GIthub node-gyp](https://github.com/nodejs/node-gyp#readme)
6. [node-sass](https://github.com/sass/node-sass)
7. [sass lang](https://sass-lang.com)
8. [Github node-gyp](https://github.com/nodejs/node-gyp)
9. 

