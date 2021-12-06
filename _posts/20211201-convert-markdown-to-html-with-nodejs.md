---
title: 构建一个转换 Markdown 文件的 Node.js 小工具
date: 2021-11-30 22:51:49
keywords: Node.js, Markdown, Markdown to html
description: 

---

本文是上一篇文章 [基于 Node.js 写一个命令行工具](http://www.edulinks.cn/2021/11/28/20211128-npm-cli-module-develop/) 的续篇，在上一篇文章中我们了解到构建一个 Node.js 命令行工具的主要步骤。本篇在此基础上，完成我们主要的业务代码编写。

这个工具的主要功能是将本地的 Markdown 文件转换为 HTML 文件。

## markdown-it

markdown-it 是一个基于 Node.js 的 Markdown 文件解析器，拥有很好的活跃度，文章发布时的最新版本是 12.0.0 ，拥有每周200万的下载量。markdown-it 的使用也非常简单，创建 markdown-it 对象后，将需要转换的文件内容传递给 render 函数，就能够输出转换后的 html 内容。更详细的使用方式还请大家参考它的官网，本文不再多说。

## 源码结构

全部的源代码放在了 [popsite]() 上，感兴趣的同学可以下载。

```sh
$ popsite git:(main) tree -I node_modules .
.
├── LICENSE
├── README.md
├── _config.yml
├── bin
│   └── pop.js
├── lib
│   ├── cli.js
│   └── popsite
│       └── index.js
├── package-lock.json
└── package.json

4 directories, 10 files
```

源码的结构比较简单，`bin/pop.js` 是入口文件，`cli.js` 主要负责处理命令行参数，主要的功能写在 `popsite/index.js` 中。

## `cli.js`

```javascript
#!/usr/bin/env node
'use strict'

const { program } = require('commander');
const Promise = require('bluebird');
// const { program } = require('commander');
const findPkg = require('./find_pkg');
const Popsite = require('./popsite/index');
const fs = require('fs')

require('./popsite/index');

function entry(cwd = process.cwd(), args){
    process.title = 'popsite';
    var current_path = process.cwd();
    // 0 is node command, 1 is the entry file
    var file_name = process.argv.slice(2)

    program
        .version(require('../package.json').version)
        .usage('<markdown_file> [--markdown <markdown_file>] [--output <html_file>]')
        .option('-m, --markdown <markdown-file>', 'Specify Markdown File')
        .option('-o, --output <html-file>', 'Specify Output Html File')
        .option('-d, --debug', 'Debug Mode')
        .description('Another static makrdown site tool.')
        .parse(process.argv)
        .action(function(option){
            console.log("Action");
        })

    // console.log(program.getOptionValue('debug'));
    console.log(program.getOptionValue('output'));

    if( program.getOptionValue('markdown') ) {
        var makrdown_file = program.opts().markdown;

        Popsite.markdown2html(makrdown_file)
    }
}

// Convert Markdown To HTML
function markdown_to_html(){

}

function loadModule(path, args) {
    return Promise.try(() => {
        console.log("loadModule .. . .")
        return new Pop(path, args)
    });
}

module.exports = entry;

```

## `popsite/index.js`

```javascript
'use strict'

const fs = require('fs')
const md = require('markdown-it')({
    html: true,
    linkify: true,
    typographer: true,
  });

var Popsite = {
    markdown2html : function( file_name, html_file = 'default.html' ){

        console.log("Begin Markdown to html, filename : " + file_name)
        // markdown_content = 
        // const content = fs.readFile(file_name, 'utf8');
        // console.log(content);

        fs.readFile( file_name, function(err, data){
            if( err ){
                // console.log(__dirname + '/' + file_name);
                console.log( file_name + ' does not exists! ');
            }else{
                console.log(data)
                var html_content = md.render(data.toString());

                fs.writeFile(html_file, html_content, 'utf-8', (err, data) => {
                    if( err ) {
                        console.log('Save Html File Error!');
                    }else{
                        console.log('Save html file success.');
                    }
                });

            }
        })
        // console.log(markdown_content)
    }
}

module.exports = Popsite;
```

## 用法

```sh
$ pop -m README.md
```

使用上面的命令，会将 README.md 文件转换为 html 并保存为 `default.html` 到当前目录下。

程序实现的功能非常简单，但是通过这个过程熟悉了 nodejs 命令行工具的整体思路，剩下的就是丰富业务逻辑功能的工作了。

## 参考资料

1. [markdown-it @ github](https://github.com/markdown-it/markdown-it#readme)
2. [Convert Markdown to HTML with Node.js](https://www.devextent.com/convert-markdown-to-html-nodejs/)
3. [使用markdown-it插件](https://blog.csdn.net/qq_45138936/article/details/105453817)
4. [markdown-it 的解析过程](https://www.jianshu.com/p/fb0ee355915c)
5. [Commander写自己的Nodejs命令](http://blog.fens.me/nodejs-commander/)
