---
title: 超越按钮，拥抱触摸界面
date: 2013-05-25 00:07:01
tag: 
keywords: 触摸界面, 触摸屏, 触摸屏设计
description: 苹果iPhone手机的发布，可以说带来了一个全新的“触摸”时代。本文介绍触摸屏为我们带来的一些变化。
---


苹果iPhone手机的发布，可以说带来了一个全新的“触摸”时代。现如今，就连小孩子都能够非常自然的使用一些触摸设备，手机、iPad等等。父母们一定很惊奇孩子们能够如此快的学会这些设备的使用方式，从这一点来看触屏设备的界面和交互设计中隐藏着很多种可以让使用体验更加简单和有趣的要点。

**菜单栏和按钮的挑战**

苹果发布的“[Human Interface Guidelines](http://developer.apple.com/library/ios/#documentation/userexperience/conceptual/mobilehig/)”以及苹果应用审核委员会，对数以万计的应用产生了重要的影响。它帮助众多的设计师和程序员理解了移动界面设计中的核心UI。例如苹果一直推荐的[UITabBar](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBar_Class/Reference/Reference.html)和[UINavigationBar](http://developer.apple.com/library/ios/#documentation/uikit/reference/UINavigationBar_Class/Reference/UINavigationBar.html)。然而实际上我们设计或制作的第一个应用可能并没有包含顶部或底部导航，手机屏幕的尺寸是如此珍贵，如果设计了顶部和底部导航，则会占据屏幕20%的内容。我们需要记住的一条原则是：**用尽可能多的空间展示内容**。

在这个充满创新的行业中，设计师需要时间去创造出更有创意、也更加原本的用户界面。也许正是苹果应用审核委员会的拒绝评语“**think out of the box”，**才能激发出象[Clear](http://www.realmacsoftware.com/clear/)和[Rise](http://www.simplebots.co/)这样的应用，让我们看到触屏界面设计的一些希望。

![](/20130525-touch-screen-design/25000558-56b73043816c4426912315a2c7eedfcf.png)

**手势驱动界面的威力**

在两年以前，我在探索手势如何才能为移动应用用户界面带来价值时总结了一条规律，“**手势的使用必须自然而然**”。这也就是为什么 Loren Brichter的“[向下拖动刷新](http://www.macstories.net/news/loren-brichter-talks-about-pull-to-refresh-patent-and-design-process/)”几乎成为了行业的一个标准。

**清除UI障碍**

开始设计一个更加手势驱动的界面，你面临的最大问题就是“**不要总是觉得应该把一些导航元素弄成可见的**”。相反，我们可以想象一个2-D或者3-D的屏幕空间，可以考虑将导航放在上、下、左、右任何一个位置，设计一个轻轻拖动或者扫过的手势，导航出现。以Facebook和Gmail的iOS版来说，我觉得最重要的一个特性就是“side-swing”菜单。这个UI概念非常易于使用。这样做的好处一方面在于能够让App的显示更加注重内容，另一方面也使得触发另外一个界面只需要一步或者两步操作就可以实现。

![](/20130525-touch-screen-design/25000609-e100782c23134c3aa570781f1cd397a9.png)

除了界面的导航，App中的内容有时也需要与用户进行互动。在所有内容下放摆放三个按钮，一定会将界面弄的混乱。这时才是使用手势的最佳时机。考虑一下双击屏幕或者长按屏幕。Instagram中，双击屏幕可以对一个内容进行Like或者Unlike。所以别再犹豫了，尽量的使用手势而不是传统的导航控件吧。

**具有适应性的界面**

对于一个创新的App来说，预测用户的行为通常是非常困难的。作者在做Belgium's Public Radio的时候，一直困惑于应该为用户提供什么样的界面，最终，决定提供三种不同的界面布局形式，而布局的调整可以通过拖动图标来实现。

![](/20130525-touch-screen-design/25000618-15478e8a2f5c4fbebce224a2325066a5.png)

**仔细考虑时间、空间和动画**

用户轻拍一个项目时会触发什么动作？如何对这个动作实现可视化？进入视窗的速度是多少？应该自动消失吗？

触摸设备的出现极大的改变了我们设计交互的方式，与传统的思考屏幕和页面的角度不同，我们需要更多的考虑时间、空间和动画这三者的结合。那么你一定会发现，设计并向同事展示这些交互概念，还采用传统的静态页面的方式已经远远不行了。一些原型工具，例如[Pop](http://popapp.in/)和[Invision](http://www.invisionapp.com/)，能够创作具有动态效果的原型。这样就非常容易来测试程序的流程，并且来**检验用户会卡在什么地方**。这样做充满了实验主义和创新精神，在向客户进行展示时，不再使用传统的静态页面和简单导航，而是将客户邀请进来参与整个交互过程。这样做会增加预算、延长工期，但是如果客户允许你这么做，那一切都不是问题了。

![](/20130525-touch-screen-design/25000628-77d37879c6804535a7758a803a1b3e00.png)

**学习曲线**

在设计手势驱动的用户界面时，移走传统的导航元素，用户的学习曲线就会对应上升。如果没有适当的用户引导，很可能用户会茫然不知所措。一点小小的探索没有关系，但是必须让用户清楚从哪里开始。很多应用在第一次运行时都有一个介绍，我觉得开场的介绍应该只介绍哪些最重要的交互方式，不要所有的东西都进行解释，那多数用户都会跳过这个步骤。也可以采用另外一种方式进行引导，再用户第一次要用到某个特性的时候进行介绍，Youtube的应用中就有这样的例子。

![](/20130525-touch-screen-design/25000637-cbaf70c4bc87454b9e4b760325717801.png)

Stop Talking，Start Making

参考资料：
1、[Beyond the Button: Embracing The Gesture-Driven Interface](http://uxdesign.smashingmagazine.com/2013/05/24/gesture-driven-interface/)
2、[iOS Human Interface Guidelines](http://developer.apple.com/library/ios/#documentation/userexperience/conceptual/mobilehig/)
3、[Touch Gesture Reference Guide](http://www.lukew.com/ff/entry.asp?1071)
4、[If you see a UI walkthrough, they blew it](http://blog.maxrudberg.com/post/38958984259/if-you-see-a-ui-walkthrough-they-blew-it)
