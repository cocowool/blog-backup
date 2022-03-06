---
title: 修改 Github 提交代码时的认证方式
date: 2022-03-06 15:57:46
keywords: GitHub , git push
description: Github 从2021年8月13日之后，不再接受纯账户加密码的形式来进行 git 相关的交互操作，必须改成基于令牌的认证方式。
---

有一个 Github 代码仓库，提交时报下面的错误

```sh
$ git push
Username for 'https://github.com': cocowool@qq.com
Password for 'https://cocowool@qq.com@github.com': 
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: Authentication failed for 'https://github.com/cocowool/wave.git/'
```

这是因为 Github 从2021年8月13日之后，在相关操作身份验证时，不再接受纯账户加密码的形式（因为不太安全），强制要求开发者使用基于令牌的认证方式。

根据 Github 的官方文档 [创建个人访问令牌](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token) 我们可以在 Settings -> Developer Settings -> Personal access tokens 中生成特定权限和有效期的令牌，生成后将令牌复制下来以备后面步骤使用。

重新设置 git 的全局认证方式

```sh
$ git config --global --unset credential.helper
```

然后再重新 `git push` 的时候，在密码栏位输入从 Github 页面获取的 Token 就可以。

```sh
$ git push
Username for 'https://github.com': cocowool@qq.com
Password for 'https://cocowool@qq.com@github.com': 
Enumerating objects: 14, done.
Counting objects: 100% (14/14), done.
Delta compression using up to 12 threads
Compressing objects: 100% (10/10), done.
Writing objects: 100% (10/10), 1016 bytes | 1016.00 KiB/s, done.
Total 10 (delta 6), reused 0 (delta 0)
remote: Resolving deltas: 100% (6/6), completed with 3 local objects.
To https://github.com/cocowool/wave.git
   f6664a5..5f663bd  main -> main
```



## 参考资料

1. [解决GitHub密码授权访问即将失效的问题](https://blog.csdn.net/u012855229/article/details/117456925)
2. [github使用pull或push失效，需要改用token登录的流程](https://blog.csdn.net/weixin_39754100/article/details/119713021)
