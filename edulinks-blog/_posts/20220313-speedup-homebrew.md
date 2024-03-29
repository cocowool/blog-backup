---
title: Homebrew 国内使用加速
date: 2022-03-13 21:23:26
keywords: Homebrew , Homebrew 加速 , Homebrew macOS
description: 本文介绍使用国内镜像站点加速 Homebrew 安装速度的方法。

---

可以选择的源：

* 清华大学 mirrors.tuna.tsinghua.edu.cn
* 中科大 mirrors.ustc.edu.cn

替换 Homebrew 默认源

```sh
# 替换 brew.git
cd "$(brew --repo)"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

# 替换 homebrew-core.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

brew update
```

替换 Home-brew Bottles 源

```sh
#对于bash用户
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile

#对于zsh用户
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

测试下速度，果然飞起了

```sh
$ brew install wget
```

恢复默认设置的办法

```sh
# 重置brew.git:
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git

# 重置homebrew-core.git:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git

brew update
```

> 2023-05-04 更新，最近在安装的时候经常遇到清华源有 404 的错误，更换为中科大源之后速度恢复正常

## 参考资料

1. [mac上安装brew如何加速](https://www.cnblogs.com/syw-home/p/12677586.html)
2. [Homebrew国内加速](https://blog.csdn.net/msatergz/article/details/93241764)
3. [镜像助手](https://brew.idayer.com/guide/change-source/)
