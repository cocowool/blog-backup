---
title: Composer使用体验
date: 2017-07-13 16:54:01
tag: 
keywords: php composer, php, php linux
description: Composer是PHP的包依赖管理器。
---


### 什么是Composer
简单来说，Composer是PHP的包依赖管理器。但是Composer并不是类似于Yum、Apt的包管理器。Composer可以用于包或者第三方库的安装，但是可以选择在Project范围内进行安装。Composer是受到Node的[NPM](https://npmjs.org/)启发。
Composer可以在下面的方面帮助到你：

1. 允许我们定义项目 Project 依赖的第三方库
2. 自动帮我们查找到哪些库可以下载，并且下载安装

### 安装(Linux/Unix/mac)
> Composer 需要PHP 5.3.2+ 版本的支持

Composer的安装文件很简单，直接下载一个PHP文件，然后在命令行执行就可以。安装过程实际上是下载一个 composer.phar 的文件到项目文件夹。这个文件是 Composer 的库文件。
```php
php composer-setup.php    //安装，下载composer.phar文件，默认到当前目录
php composer.phar //运行composer
```

### 使用

#### 配置 composer.json
想在项目中使用 Composer 首先需要一个 composer.json 文件。该文件为必需文件，该文件指定了项目依赖哪些第三方库。
```php
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```
Composer 会根据 composer.json 的配置从 资源库 中查找。资源库可以是 Packagist 或者指定的资源库。

#### 安装
```php
php composer.phar install
```
执行上面的命令，可以将monolog安装（下载）到项目中。下载完成后，项目中多一个 composer.lock 文件以及一个 vendor 文件夹。vendor 文件夹中包含了下载的 monolog 库以及一个 autoload.php、composer文件夹。
第一次安装时，Composer 根据 composer.json 文件来决定需要哪些依赖，之后会将安装过程中的相关信息收集写入 composer.lock 文件，该文件将我们依赖的第三方库的版本进行锁定。composer.lock 文件应当提交到版本库中，以确保其他用户都使用相同的第三方库。

#### Autoload
```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
```
Composer 会产生一个 autoload.php 文件，代码中包含该文件后就可以正常使用第三方库。也可以自己在 composer.json 中配置 Autoload的信息。
```javascript
{
    "autoload": {
        "psr-4": {"Acme\\": "src/“}    //注册psr-4的自动加载器，命名空间为 Acme
    }
}
```
配置之后，执行```php composer.phar dump-autoload```来重新生成autoload。

#### 更新第三方库到最新版本
```php
php composer.phar update
```
这个命令可以将项目中的第三方库进行更新，更新到最新版本，依赖于 composer.json 的配置。同时更新 composer.lock 文件。

参考资料：
1、[Composer](https://getcomposer.org)