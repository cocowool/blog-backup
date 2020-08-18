---
title: 由于版本依赖造成的YUM段错误
date: 2012-02-25 23:12:01
tag: 
---

最近在服务器（Centos 5.3，64位）上使用YUM，总是提示 Segmentation Fault，无论执行什么命令都是如此，一时不得其解。


查了一些资料，大体上说是由于Zlib版本造成的。查看了一下，发现最近确实安装了zlib的1.2.5版本，而造成了YUM的依赖问题。


网上资料中显示问题排查时使用到 strace yum update，但是我看到提示并不能够判断出问题所在，看来还需要深入理解才行。


尝试重新编译安装了zlib1.2.3，但是结果还是段错误。仔细一看，发现zlib其实并没有将so安装到/usr/local/lib目录下，在/usr/lib下搜索了一下，找到了这个so，不清楚是什么时候安装的，拷贝到/usr/local/lib目录下，然后重新进行了一次编译安装。


完成之后执行 yum clean all，yum指令就可以正常使用了。


总结：问题应该出在zlib的版本更新上，但是应该和zlib软件本身的代码没什么关系，只是在软链接的配置上的改变，对yum造成了影响。


参考资料：
1、[yum segmentation fault in centos](http://serverfault.com/questions/256385/yum-segmentation-fault-in-centos)
2、[YUM段错误Centos Segmentation Fault](http://qiaojing.blog.51cto.com/841363/649160)












