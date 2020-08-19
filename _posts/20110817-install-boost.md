---
title: Linux上安装Boost C++ Libraries
date: 2011-08-17 11:01:01
tag: 
---

**Boost C++ 库**（Libraries）是一组扩充[C++](http://zh.wikipedia.org/wiki/C%2B%2B)功能性的经过[同行评审](http://zh.wikipedia.org/wiki/%E5%90%8C%E8%A1%8C%E8%A9%95%E5%AF%A9)（Peer-reviewed）且[开放源代码](http://zh.wikipedia.org/wiki/%E9%96%8B%E6%94%BE%E6%BA%90%E7%A2%BC)程序库。大多数的函数为了能够以开放源代码、封闭项目的方式运作，而授权于Boost软件授权条款（Boost Software License）之下。许多Boost的开发人员是来自C++标准委员会，而部份的Boost库成为C++的[TR1](http://zh.wikipedia.org/wiki/C%2B%2B_Technical_Report_1)标准之一。[[1]](http://zh.wikipedia.org/wiki/Boost_C%2B%2B_Libraries#cite_note-0)
为了要确保库的效率与弹性，Boost广泛的使用[模板](http://zh.wikipedia.org/wiki/%E6%A8%A1%E6%9D%BF_(C%2B%2B))（template）功能。而它是针对各式领域的C++用户与应用领域（Application Domain）上，包含的库类从像[smart_ptr 库](http://www.boost.org/doc/libs/1_35_0/libs/smart_ptr/smart_ptr.htm)这种类通用库，到像是[文件系统](http://zh.wikipedia.org/w/index.php?title=Boost_%E6%AA%94%E6%A1%88%E7%B3%BB%E7%B5%B1&action=edit&redlink=1)的[操作系统](http://zh.wikipedia.org/wiki/%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1)抽象层，甚至能够利用Boost来开发额外的库或是给高级的C++用户利用，像是[MPL](http://www.boost.org/libs/mpl)。


Boost的安装
参考官方的安装文档，利用 Easy Build and Install 可以进行方便的安装。
Issue the following commands in the shell (don't type $; that represents the shell's prompt):


$ cd path/to/boost_1_47_0
$ ./bootstrap.sh --help
Select your configuration options and invoke ./bootstrap.sh again without the --help option. Unless you have write permission in your system's /usr/local/ directory, you'll probably want to at least use


$ ./bootstrap.sh --prefix=path/to/installation/prefix
to install somewhere else. Also, consider using the --show-libraries and --with-libraries=library-name-list options to limit the long wait you'll experience if you build everything. Finally,


$ ./b2 install
will leave Boost binaries in the lib/ subdirectory of your installation prefix. You will also find a copy of the Boost headers in the include/ subdirectory of the installation prefix, so you can henceforth use that directory as an #include path in place of the Boost root directory.


以上是1.47的安装方法，1.39也有类似的方法，但是生成的二进制程序叫做 bjam 而不是 b2。


参考资料：
1、[Boost C++ Libraries](http://zh.wikipedia.org/wiki/Boost_C%2B%2B_Libraries)
2、[Get Started with Boost](http://www.boost.org/doc/libs/1_47_0/more/getting_started/unix-variants.html#get-boost)
3、[Boost 1.39 编译安装手记](http://blog.csdn.net/cnhome/article/details/4459497)













