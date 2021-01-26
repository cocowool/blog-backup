---
title: Phonegap项目中禁用WebViewBounce
date: 2013-07-07 14:59:01
tag: 
keywords: phonegap, webviewbounce, 禁用webviewbounce
description: UIWebView是iOS SDK中一个最常用的控件，在PhoneGap中，默认也是使用UIWebView作为默认视图显示我们的HTML应用的。
---


UIWebView是iOS SDK中一个最常用的控件，在PhoneGap中，默认也是使用UIWebView作为默认视图显示我们的HTML应用的。

在使用PhoneGap的项目中，默认WebViewBounce这个选项是打开的，所以使用手指向下或者向上滑动屏幕时，经常会看到页面底部和屏幕底部会出现一大片空白，然后松开手指后，再弹回去的特效。

之前查了很多资料，说可以通过修改 plist 或者 config.xml 来关闭这个效果，如下：

```xml
<preferencename="WebViewBounce"value="false"/>
<preferencename="DisallowOverscroll"value="true"/>
```

经过我在 PhoneGap 2.6、PhoneGap 2.9 这两个版本中测试后，发现都不能够生效。经过分析，实际上iOS中的PhoneGap项目，只不过是默认初始化一个UIWebView，然后在这个视图中加在HTML资源，那么我们可以直接使用原生代码将Bounce禁用。如下所示：

```objective-c
- (void)webViewDidFinishLoad:(UIWebView*)theWebView
{
  // Black base color for background matches the native apps
  theWebView.backgroundColor= [UIColorblackColor];
  [(UIScrollView*)[[theWebViewsubviews]objectAtIndex:0]setBounces:NO];

  return[superwebViewDidFinishLoad:theWebView];
}
```

再重新编译之后，可以看到效果了吧 :)

参考资料：
1、[Project Settings for iOS](https://github.com/apache/cordova-docs/blob/master/docs/en/edge/guide/project-settings/ios/index.md)
2、[What's new in Cordova iOS 2.6.0](http://shazronatadobe.wordpress.com/2013/04/08/whats-new-in-cordova-ios-2-6-0/)
3、[iOS开发之UIWebView](http://www.cnblogs.com/zhuqil/archive/2011/07/28/2119923.html)
4、[UIWebView禁止Bounce回弹](http://blog.sina.com.cn/s/blog_8c87ba3b0100u89h.html)