---
title: 利用MapabcAPI实现基于浏览器的地理定位
date: 2010-08-31 00:35:01
tag: 
---

现在互联网中有了越来越多的地理位置服务，正在制定中的HTML5规范也试图在浏览器中内置嵌入地理位置共享服务，而且幸运的是，我们已经能够在Firefox 3.5+、Chrome 5.0+、Opera 10.60+、Safari 5.0+ 以及一些移动终端，注入IPhone、Android上使用这项服务，那么这个服务能否有Mapabc的API结合，来实现用户的地理定位呢？
传统上，地图应用上为用户定位的方式一般是：
> 用户访问时获得用户的IP。
用这个IP到IP库中匹配用户的城市信息。
根据获取的城市信息去已有的城市中心点字典中进行匹配。
匹配完成后将用户地图的中心点进行相应的切换。

这个过程繁琐，而且准确性极大程度上依赖于我们IP库的准确程度。现在有了浏览器的位置共享服务，我们可以更容易的实现用户的地理定位。
**Geolocation对象介绍**
正如开头所讲的，到目前为止，W3C中deGeolocation API规范已经在非IE的浏览中得到了实现。规范中提到，浏览器提供位置信息的来源是不固定的，可能是GPS、也可能来自于IP地址、RFID、WiFi、蓝牙或者GSM\CDMA的定位信息，也不保证返回的结果一定准确。Geolocation的API同时对“只获得一次”定位和“持续监督”定位都做出了规定。
**Geolocation的一些方法**
Geolocation对象位于浏览器Navigator对象下，可以通过 navigator.geolcation 来访问，不支持 geolocation 的浏览器不会包含这一对象，不会造成错误，可以用来做浏览器的区分。
> if ( navigator.geolcation ){
alert(‘ Your Browser dosent support geolcocation ');
} else{
alert(‘ Your Browser support geolocation’);
}

在访问 geolocation 对象时，浏览器会进行提示，询问用户使用许可对网站提供位置服务，得到用户的许可后，访问才会继续，否则会被停止。而且，我们可以捕捉到用户同意或者拒绝的事件。
在获得用户的学科之后，就可以利用Geolocation获取当前位置，核心方法如下：
> navigator.geolocation.getCurrentPosition( getPositionSuccess , getPositionError );

上面的代码中，调用了 getCurrentPosition 方法，并且传递了两个回调函数的参数，分别用来处理位置获取成功和失败时的情况。实事上，这个函数还可以接受第三个参数，用来传入一些配置信息，包括对于超时的设置等。下面来看两个获取成功和获取失败时，回调函数的处理。
> 获取地理信息成功的错误处理信息
function getPositionSuccess ( position ) {
var lat = position.coords.latitude;
var lng = position.coords.longitude;
alert( ‘Now you are lat – lng ‘);
}

获取地理位置失败的回调函数
function getPositionError( error ){
switch(error.code)
case error.TIMEOUT;
//Do something
break;
case error.PERMISSION_DENIED;
//Do something
break;
case error.POSITION_UNAVAILIABLE;
//Do something
break;
}

获取地理位置信息的配置变量。在填入获取地理位置信息变量时，能够对获取地理位置的一些细节进行影响。下面是目前包括的几个具体参数
> In ECMAScript, the```enableHighAccuracy```,```timeout```and```maximumAge```properties are all optional: when creating a PositionOptions object, the developer may specify any of these properties.

刚才也提到，我们还可以对地理位置进行持续的追踪，这是需要用到 watchPosition 方法。
因为对于移动设备的用户来说，位置并不是固定的，我们需要来处理这种移动客户端的情况，而不是每次要提醒用户去刷新浏览器来更新位置。
> navigator.geolocation.watchPosition( refreshPosition );

**与Mapabc API的整合**
看完上面的关于 geolocation 的介绍之后，我们就可以将这个服务与Mapabc API一起，整合到我们的应用中了。下面是一个具体的实现样例，供参考：
> <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "[http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"](http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd")>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<div>
<div id="mapobj" style="width:500px; height:500px; border:1px solid black;"></div>
<div id="info"></div>
<input type="button" id="getPos" value="Get My Position" />
</div>
<script src="[http://app.mapabc.com/apis?&t=flashmap&v=2.3.4&key=0c3235346b1e00772eafe1c099f4b23fe5ec1202d393f952395e628567934baceedcaf1508ea044f"](http://app.mapabc.com/apis?&t=flashmap&v=2.3.4&key=0c3235346b1e00772eafe1c099f4b23fe5ec1202d393f952395e628567934baceedcaf1508ea044f")type="text/javascript"></script>
<script type="text/javascript">
function getPositionSuccess( position ){
var lat = position.coords.latitude;
var lng = position.coords.longitude;
//alert( "您所在的位置： 经度" + lat + "，纬度" + lng );
var mapOptions = new MMapOptions();//构建地图辅助类
mapOptions.zoom=13;//设置地图zoom级别
mapOptions.center=new MLngLat(lng,lat);   //设置地图中心点
mapOptions.toolbar = MINI;//工具条
mapOptions.toolbarPos = new MPoint(15,15);  //工具条偏移
mapOptions.overviewMap = SHOW;//是否显示鹰眼
mapOptions.scale = SHOW;//是否显示比例尺
mapOptions.returnCoordType = COORD_TYPE_OFFSET;//返回偏移明码坐标
mapOptions.zoomBox = true;//鼠标滚轮缩放和双击放大时是否有红框动画效果。
mapabc.map=new MMap("mapobj",mapOptions); //地图初始
if(typeof position.address !== "undefined"){
var country = position.address.country;
var province = position.address.region;
var city = position.address.city;
alert(' 您位于 ' + country + province + '省' + city +'市');
}
}
function getPositionError( error ){
switch(error.code){
case error.TIMEOUT :
alert( " 连接超时，请重试 " );
break;
case error.PERMISSION_DENIED :
alert( " 您拒绝了使用位置共享服务，查询已取消 " );
break;
case error.POSITION_UNAVAILABLE :
alert( " 亲爱的火星网友，非常抱歉，我们暂时无法为您所在的星球提供位置服务 " );
break;
}
}
var dom = {
btn    : document.getElementById("getPos"),
info: document.getElementById("info"),
map    : document.getElementById("mapObj")
};
var mapabc = {
map        : null,
marker    : null
};
dom.btn.onclick = function(){
if( navigator.geolocation ){
dom.info.innerHTML = "Waiting for the geolocation result...";
navigator.geolocation.getCurrentPosition( getPositionSuccess, getPositionError );
} else {
dom.info.innerHTML = "Your browser doesn't support geolocation.";
}
}
</script>
</body>
</html>

上面的代码，会在地图显示后询问用户是否共享位置信息，如果共享则会切换地图中心点到相应的位置。当然，我们可以在自己的应用中更加灵活的使用位置共享服务，既然有了这么 Cool 的服务支持，我们还等什么呢。



参考资料：
1、[Blueidea BBS](http://bbs.blueidea.com/thread-2995481-1-1.html)
2、[Geolocation API Specification](http://dev.w3.org/geo/api/spec-source.html)
3、[Mapabc API](http://code.mapabc.com/)












