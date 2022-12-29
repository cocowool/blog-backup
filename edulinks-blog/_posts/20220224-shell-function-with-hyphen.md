---
title: Shell 函数带中横线问题排查
date: 2022-02-24 18:42:11
keywords: shell , shell 函数 , shell 函数中横线 , shell hyphen
description: Shell 中的函数名称如果包含中横线，在某些 shell 环境环境下执行会报错，本文探索了如何解决该问题，以及在 Python 环境下如何解决该问题。
---

Shell 中编写的函数，如果函数名中带了中横线，在使用 `/bin/sh` 执行时会报错。

```sh
➜  subprocess git:(master) ✗ cat kubectl.sh      

_kubectl_api-resources()
{
    hostname
}

_kubectl_api-resources
➜  subprocess git:(master) ✗ sh kubectl.sh
kubectl.sh: line 5: `_kubectl_api-resources': not a valid identifier
```

根据 POSIX 的标准，函数名要符合以下的规范。

> **3.231 Name**
> In the shell command language, a word consisting solely of underscores, digits, and alphabetics from the portable character set. The first character of a name is not a digit.

可以看到，中横线不在标准支持的范围内。因此使用 `/bin/sh` 执行时，会看到我们上面列出的错误。

但是 `bash` 和 `zsh` 却可以支持函数中的中横线。

```sh
➜  subprocess git:(master) ✗ cat kubectl.sh      

_kubectl_api-resources()
{
    hostname
}

_kubectl_api-resources
➜  subprocess git:(master) ✗ sh kubectl.sh
kubectl.sh: line 5: `_kubectl_api-resources': not a valid identifier
➜  subprocess git:(master) ✗ bash kubectl.sh
bogon
➜  subprocess git:(master) ✗ zsh kubectl.sh
bogon
```

因此，如果我们希望我们的 Shell 脚本具有更好的可移植性，那就尽量避免在函数命名时使用中横线。

Python 使用 subprocess.Popen 执行 shell 时，若选择了 `shell=True` 选项，则会默认使用 `/bin/sh`。因此在遇到使用了中横线的 shell 函数时，也会报错。如果希望使用 `/bin/bash` 则需要设置 `executable` 参数，示例如下：

```python
import subprocess

def bash_command(cmd):
    subprocess.Popen(cmd, shell=True, executable='/bin/bash')

bash_command('a="Apples and oranges" && echo "${a/oranges/grapes}"')
```

如果这个方法不生效，还可以使用下面的方案。

```python
import subprocess

def bash_command(cmd):
    subprocess.Popen(['/bin/bash', '-c', cmd])
```

## 参考资料

1. [Python `subprocess.Popen` documentation](https://docs.python.org/library/subprocess.html#subprocess.Popen)
2. [How to use the bash shell with Python's subprocess module install of /bin/sh](https://www.saltycrane.com/blog/2011/04/how-use-bash-shell-python-subprocess-instead-binsh/)
3. [Are there problems with hyphens in functions, aliases, and executables?](https://unix.stackexchange.com/questions/168221/are-there-problems-with-hyphens-in-functions-aliases-and-executables)
