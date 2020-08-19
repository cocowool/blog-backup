---
title: Ajax应用中CKEDITOR多实例问题的解决
date: 2010-09-04 13:27:01
tag: 
---

著名的Fckeditor升级之后就改名叫了CKEDITOR，界面和功能有了很大的提升，所以我在最近的项目中应用CKEDITOR。随着项目的深入，在Ajax应用较多的一个部分使用CKEDITOR时发现了问题，描述如下：
通过单击一个链接，调入需要显示的内容，其中包括一个富文本编辑器，这里使用了CKEDITOR。第一次调入时，一切正常。噩梦从第二次开始，在Firefox3.5+中，第二次的时候提示 i.contentWindow is null ，而在IE系列中则是一些模糊的错误提示。
出现这个问题后，分析的方向一直集中在CKEDITOR多实例的问题。因为第一次点击链接，调用内容时，已经进行了一次CKEDITOR的实例化，第二次调入时必然会与第一次的冲突。查阅了CKEDITOR的样例资料，试了下面的方法：
> if( editor ){
editor.destroy();
editor = null;
}
editor = CKEDITOR.replace(‘content’);

但是没有用，问题依然发生。后来google之，得到了目前可以正常解决这个问题的方法
> if( CKEDITOR.instances['content'] ){
CKEDITOR.remove(CKEDITOR.instances['content']);
}

CKDEITOR.replace(‘content’);

应用了这个方法之后，不管是多少次调用，都不会再发生错误，应该就是正确的解决方法了，但是可惜的是在CKEDITOR的官方文档和论坛中均为看到相关的说明，遗憾…
Technorati 标签:[Ajax](http://technorati.com/tags/Ajax),[CKEDITOR](http://technorati.com/tags/CKEDITOR),[instance](http://technorati.com/tags/instance)
参考资料：
1、[CKEDITOR](http://www.ckeditor.com)
2、[CKEDITOR instance already exists](http://stackoverflow.com/questions/1794219/ckeditor-instance-already-exists)












