---
title: 如何开发YUI3的扩展
date: 2012-06-01 13:38:01
tag: 
keywords: yui3, yui3扩展, css
description: YUI是Yahoo发布的一个JS框架，本文介绍如何开发YUI3的扩展。
---

YUI是Yahoo发布的一个JS框架，虽然不如jQuery简单，但是如果你是要做一些复杂的事情的时候，有一个合适量级的框架还是能有用不少。

YUI3中的一些定义。
Module。YUI3中的每块代码都可以看作是一个Module。

```javascript
YUI().use('node',function(Y){

Y.one('div.status').setContent('Ready');

});
```

使用YUI，我们需要一个YUI实例，这个实例称之为Sandbox，之后我们所有的代码都在这个Sandbox中运行，不会与其他的代码冲突，这样也可以解决遗留代码（Legacy Code）的冲突问题。函数中的Y是对这个Sandbox实例的引用。

还有其他的使用方式：
```javascript
varY = newYUI();

Y.use('node'),function(Y){

Y.one('div.status').setContent('Ready');

});
```

或者
```javascript
(newYUI).use('node'),function(Y){

Y.one('div.status').setContent('Ready');

});
```

在YUI中注册Module的三种方式。
1. By Seed；
2. By Includsion；
3. By config。

1、By Seed。通过引入Seed文件后，由YUI框架自动调入。
```html
<script src=”http://yui.yahooapis.../yui-min.js”></script>

<script>

YUI().use (‘node’, ‘anim’,function(Y) {

newY.Anim({ node:'#demo', to: {opacity: 0} });

Y.one (‘div.status’).setContent (‘ready!’);

});

</script>
```

2、By Inclusion。在页面文件中显式的引入。
```html
<script src=”http://yui.yahooapis.../yui-min.js”></script>

<script src=” http://yui.yahooapi.../build/gallery-yql/gallery-yql.js”></script>

<script>

YUI().use (‘gallery-yql’,function(Y) {

newY.yql('select * from github.user.info where (id=”caridy")',function(r) {

r.query;// The result

});

});

</script>
```

3、By Configuration。在配置文件中指定引入Module所需要的信息，这是一种比较推荐的使用方式。在YUI实例化时，YUI()方法中可以接收多个配置项，例如YUI(c1,c2,,,)。

```javascript
<script src=”http://yui.yahooapis.../yui-min.js”></script><script>
YUI({
	modules: {
    'gallery-yql': {
      fullpath:'http://yui.yahooapi.../build/gallery-yql/gallery-yql.js',
      requires: ["get","event-custom"]
    }
	}
}).use (‘gallery-yql’,function(Y) {
	newY.yql('select * from github.user.info where (id=”caridy")',function(r) {
		r.query;// The result
	});
});
</script>
```


如何编写一个YUI的扩展
1、实用工具，也是最简单的方式。
```javascript
YUI.add(‘gallery-youtube’,function(Y) {
	Y.youtube = {
		play:function(node, vid) {
			/* … */
		}
	};
}, ‘0.0.1’, { requires: [‘node’] });
```

2、使用类的形式，这也是比较常用的形式。
```javascript
YUI.add('gallery-slideshow',function(Y){

varSlideshow =function(config){

Slideshow.superclass.constructor.apply(this,arguments);

}

Slideshow.hello =function(){

alert("Hello world!");

}

Y.Slideshow = Slideshow;
},'0.0.1', {requires:['node']});
```

有了这些知识，我们就可以创建一些自己的Module来减轻Copy & Paste的工作量了，哈哈。

参考资料：
1、[Building YUI3 Custom Modules](http://www.slideshare.net/caridy/building-yui-3-custom-modules)