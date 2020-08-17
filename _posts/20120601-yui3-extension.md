---
title: 如何开发YUI3的扩展
date: 2012-06-01 13:38:01
tag: 
---

YUI是Yahoo发布的一个JS框架，虽然不如jQuery简单，但是如果你是要做一些复杂的事情的时候，有一个合适量级的框架还是能有用不少。
YUI3中的一些定义。
Module。YUI3中的每块代码都可以看作是一个Module。
1:YUI().use('node',function(Y){
2:
3:Y.one('div.status').setContent('Ready');
4:
5:});


使用YUI，我们需要一个YUI实例，这个实例称之为Sandbox，之后我们所有的代码都在这个Sandbox中运行，不会与其他的代码冲突，这样也可以解决遗留代码（Legacy Code）的冲突问题。函数中的Y是对这个Sandbox实例的引用。
还有其他的使用方式：
1:varY =newYUI();
2:
3:Y.use('node'),function(Y){
4:
5:Y.one('div.status').setContent('Ready');
6:
7:});


或者
1:(newYUI).use('node'),function(Y){
2:
3:Y.one('div.status').setContent('Ready');
4:
5:});


在YUI中注册Module的三种方式。1、By Seed；2、By Includsion；3、By config。
1、By Seed。通过引入Seed文件后，由YUI框架自动调入。
1:<script src=”http://yui.yahooapis.../yui-min.js”></script>
2:
3:<script>
4:
5:YUI().use (‘node’, ‘anim’,function(Y) {
6:
7:newY.Anim({ node:'#demo', to: {opacity: 0} });
8:
9:Y.one (‘div.status’).setContent (‘ready!’);
10:
11:});
12:
13:</script>


2、By Inclusion。在页面文件中显式的引入。
1:<script src=”http://yui.yahooapis.../yui-min.js”></script>
2:
3:<script src=” http://yui.yahooapi.../build/gallery-yql/gallery-yql.js”></script>
4:
5:<script>
6:
7:YUI().use (‘gallery-yql’,function(Y) {
8:
9:newY.yql('select * from github.user.info where (id=”caridy")',function(r) {
10:
11:r.query;// The result
12:
13:});
14:
15:});


</script>
3、By Configuration。在配置文件中指定引入Module所需要的信息，这是一种比较推荐的使用方式。在YUI实例化时，YUI()方法中可以接收多个配置项，例如YUI(c1,c2,,,)。
1:<script src=”http://yui.yahooapis.../yui-min.js”></script><script>
2:
3:YUI({
4:
5:modules: {
6:
7:'gallery-yql': {
8:
9:fullpath:'http://yui.yahooapi.../build/gallery-yql/gallery-yql.js',
10:
11:requires: ["get","event-custom"]
12:
13:}
14:
15:}
16:
17:}).use (‘gallery-yql’,function(Y) {
18:
19:newY.yql('select * from github.user.info where (id=”caridy")',function(r) {
20:
21:r.query;// The result
22:
23:});
24:
25:});
26:
27:</script>


如何编写一个YUI的扩展
1、实用工具，也是最简单的方式。
1:YUI.add(‘gallery-youtube’,function(Y) {
2:
3:Y.youtube = {
4:
5:play:function(node, vid) {
6:
7:/* … */
8:
9:}
10:
11:};
12:
13:}, ‘0.0.1’, { requires: [‘node’] });


2、使用类的形式，这也是比较常用的形式。
1:YUI.add('gallery-slideshow',function(Y){
2:
3:varSlideshow =function(config){
4:
5:Slideshow.superclass.constructor.apply(this,arguments);
6:
7:}
8:
9:Slideshow.hello =function(){
10:
11:alert("Hello world!");
12:
13:}
14:
15:Y.Slideshow = Slideshow;
16:
17:},'0.0.1', {requires:['node']});


有了这些知识，我们就可以创建一些自己的Module来减轻Copy & Paste的工作量了，哈哈。
参考资料：
1、[Building YUI3 Custom Modules](http://www.slideshare.net/caridy/building-yui-3-custom-modules)












