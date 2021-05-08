---
title: Silverlight学习笔记：布局之stackpanel
date: 2009-12-02 21:49:01
tag: 
keywords: silverlight
description: Silverlight 中使用 layout model 进行布局的管理，我们将所有的元素都放在一个容器 Container 中。
---

在用户界面设计方面的竞争多半是围绕着如何制作出有吸引力、实用、灵活的用户界面而展开的。在基于浏览器的应用中进行界面的设计则是一项更加需要技巧的工作，因为我们的客户端总是丰富多样而又难以准确预测。幸好，Silverlight 继承了 WPF 中灵活性的界面设计的特点。
> Silverlight 中使用 layout model 进行布局的管理，我们将所有的元素都放在一个容器 Container 中。每个容器都有自己的界面逻辑，比如 stackpanel、grid、canvas等。我们甚至可以创建具有自定义逻辑的容器。所有的容器都从抽象类 System.Windows.Controls.Panel.class 继承而来。Panel 类的层级关系如下图：
![](/20091202-silverlight/image_thumb.png)

Panel 类有 Background 和 children 这两个公共的属性，这是布局开始的第一步，在 silverlight 中，最 主要的 panel 有：stackpanel、grid、canvas。这几个类我们可以在 System.Windows.Controls 中找到。Stackpanel 一般用来做水平或者垂直的排布；grid 通过行和列来布局元素，基本上就是表格布局；canvas 利用绝对的坐标来实现定位。一个典型的 silverlight ，一般是采用 grid 来进行布局，其中还可以嵌套其他的基本布局元素。

需要改变 panel 的background 属性时，我们多数会想到采用一个表示“颜色”的对象，但是 silverlight 采用了一种更为灵活的方式，brush 对象，这样就可以提供“纯色”(Solid Color)、“线性渐变”、“圆形渐变”这样的颜色效果，brush 对象存在于 System.Windows.Media 中。

例如，我们可以用一句话来改变当前 grid 的背景。
LayoutRoot.Background =newSolidColorBrush(Colors.Blue);


**布局元素的一些属性**
> 背景: Background
改变背景的方法有三种，一种通过之前示例的写 code 的方式；另外一种是直接在元素上添加 background= "#aabbcc" 这样的方式；最后一种是利用 <Grid.background></Grid.background>这样的标签必合起来，中间可以添加较为复杂的背景变化。

边框: Borders
边框的掌握就更为简单，我们只要记住了 Background、BorderBrush And BorderThickness、CornerRadius、Padding 就可以应付了。
**Stackpanel的布局方式**
stackpanel 作为一个基本的布局元素，提供了将 UIElement 进行水平或者垂直排布的功能，就像一个堆栈一样。他使用起来也非常方便，将所有的元素都放入 <Stackpanel></Stackpanel>标签中就OK了。我们可以通过调整 HoriazonAlignment 和 VerticalAlignment 来调整水平或垂直。 默认情况下，stackpanel 会占据一个容器内所有的剩余空间，如果我们不希望这样，我们可以对位于 stackpanel 内的元素分别定义他们的尺寸属性，包括 Width、Height、MinWidth、MinHeight、MaxWidth、MaxHeight。但是还有一个问题，任何用户界面的设计，都会存在一定的空间来分隔视觉上不同的元素，想要实现这个功能，我们需要使用 Margin 属性。我们可以给 Margin 设置一个单个的值，这样四个方向上的 margin 都相同，也可以按照 Margin=“5,3,2,5”，这样方向的顺序分别是 左、上、右、下，和 CSS 中的定义不同，习惯起来感觉还有点吃力。在多个元素之间计算 Margin 时，其所用的规则也和 CSS 不同，但是应该更接近于一般的理解。

![](/20091202-silverlight/image_thumb_1.png)












