---
title: Virtualbox镜像文件克隆问题
date: 2009-09-23 19:26:01
tag: 
---


今天在克隆一个虚拟机的镜像文件的时候，执行

VBoxManage clonehd centos-1.vdi centos-2.vdi

提示：
ERROR: Cannot register the hard disk '/home/cipher/Data/centos-1.vdi' with UUID {78c1f396-f92a-419b-82d2-843f34b917d2} because a hard disk '/home/cipher/Data/centos-1.vdi' with UUID {78c1f396-f92a-419b-82d2-843f34b917d2} already exists in the media registry ('/home/cipher/.VirtualBox/VirtualBox.xml')
Details: code NS_ERROR_INVALID_ARG (0x80070057), component VirtualBox, interface IVirtualBox, callee nsISupports
Context: "OpenHardDisk(Bstr(szFilenameAbs), AccessMode_ReadWrite, false, Bstr(""), false, Bstr(""), srcDisk.asOutParam())" at line 624 of file VBoxManageDisk.cpp

查找原因分析了半天，原来是这个命令不支持相对路径，修改为

VBoxManage clonehd $(pwd)/centos-1.vdi $(pwd)/centos-2.vdi

一切就正常了。

0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Clone hard disk created in format 'VDI'. UUID: a1f23700-6431-42ee-986f-cac44610a0be

参考资料：
1、[Virtualbox clonhd](http://muffinresearch.co.uk/archives/2009/07/23/virtualbox-error-cannot-register-the-hard-disk/)











