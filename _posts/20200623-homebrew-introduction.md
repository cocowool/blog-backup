---
title: Homebrew 介绍与上手指南
date: 2020-06-23 19:23:20
tags:
---

>  Homebrew是一款macOS平台下的软件包管理工具，能够利用很简单的指令，帮助我们实现对开源软件的安装、更新、卸载等常用操作，省去了我们独立下载、编译、安装、配置各种开源软件的繁琐步骤。可以类比于Linux下的`yum`或者Ubuntu的`apt-get`。

Homebrew不仅能够帮我们安装一些命令行的开源软件和工具，例如`wget/node`等等，还能够帮助我们安装macOS图形界面的应用程序，例如`Firefox`。

## 使用介绍

* 安装命令行工具

  ```sh
  $ brew install wget
  ```

* 卸载命令行工具

  ```sh
  $ brew uninstall wget
  ```

* 搜索想要安装的工具

  ```sh
  $ brew search wget
  ==> Formulae
  wget      wgetpaste                                                     
  ```

* 安装图形界面应用程序

  ```sh
  $ brew cast install firefox
  ```

* 卸载图形界面应用程序

  ```sh
  $ brew cast uninstall firefox
  ```

## 安装避坑

Homebrew的用法很简单，安装也很简单，官方网站就给了一行通用安装脚本。

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

但是现在因为众所周知的原因raw.githubusercontent.com这个域名访问会存在一些问题，可以利用[ip138](https://ip138.com)等网站解析出可以登陆的ip，然后通过修改 hosts 文件的方式来解决访问问题。但是下载速度依然会很慢，而且经常会出现下面的错误。

```sh
==> Downloading and installing Homebrew...
remote: Enumerating objects: 54, done.
remote: Counting objects: 100% (54/54), done.
remote: Compressing objects: 100% (54/54), done.
Receiving objects:   0% (504/138701), 308.01 KiB | 22.00 KiB/s   

error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 60
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed
Failed during: git fetch origin --force
==> Installation successful!
```

错误信息中明确提示了git在获取文件的过程中失败了，但是仍有brew安装成功的信息打出来，因为shell安装脚本是顺序执行的，还缺少一些安装是否成功的校验，具体看下面的代码。

```sh
ohai "Downloading and installing Homebrew..."
(
  cd "${HOMEBREW_REPOSITORY}" >/dev/null || return

  # we do it in four steps to avoid merge errors when reinstalling
  execute "git" "init" "-q"

  # "git remote add" will fail if the remote is defined in the global config
  execute "git" "config" "remote.origin.url" "${BREW_REPO}"
  execute "git" "config" "remote.origin.fetch" "+refs/heads/*:refs/remotes/origin/*"

  # ensure we don't munge line endings on checkout
  execute "git" "config" "core.autocrlf" "false"

  execute "git" "fetch" "origin" "--force"
  execute "git" "fetch" "origin" "--tags" "--force"

  execute "git" "reset" "--hard" "origin/master"

  execute "ln" "-sf" "${HOMEBREW_REPOSITORY}/bin/brew" "${HOMEBREW_PREFIX}/bin/brew"

  execute "${HOMEBREW_PREFIX}/bin/brew" "update" "--force"
)

if [[ ":${PATH}:" != *":${HOMEBREW_PREFIX}/bin:"* ]]; then
  warn "${HOMEBREW_PREFIX}/bin is not in your PATH."
fi

ohai "Installation successful!"
echo
```



## 参考资料

1. [Homebrew homepage](https://brew.sh/index_zh-cn)
2. [清华大学开源软件镜像站](https://mirror.tuna.tsinghua.edu.cn)
3. [Homebrew介绍和使用](https://www.jianshu.com/p/de6f1d2d37bf)