---
title: 利用JS实现的根据经纬度计算地球上两点之间的距离
date: 2009-03-24 13:52:01
tag: 
---

用JS实现的两个根据经纬度计算地球表面任意两点之间距离的函数。
最近用到了根据经纬度计算地球表面两点间距离的公式，然后就用JS实现了一下。
计算地球表面两点间的距离大概有两种办法。
第一种是默认地球是一个光滑的球面，然后计算任意两点间的距离，这个距离叫做大圆距离(The Great Circle Distance)。

公式如下：


使用JS来实现为：
varEARTH_RADIUS=6378137.0;//单位M
varPI=Math.PI;

functiongetRad(d){
returnd*PI/180.0;
}

/**
* caculate the great circle distance
* @param {Object} lat1
* @param {Object} lng1
* @param {Object} lat2
* @param {Object} lng2
*/
functiongetGreatCircleDistance(lat1,lng1,lat2,lng2){
varradLat1=getRad(lat1);
varradLat2=getRad(lat2);

vara=radLat1-radLat2;
varb=getRad(lng1)-getRad(lng2);

vars=2*Math.asin(Math.sqrt(Math.pow(Math.sin(a/2),2) + Math.cos(radLat1)*Math.cos(radLat2)*Math.pow(Math.sin(b/2),2)));
s=s*EARTH_RADIUS;
s=Math.round(s*10000)/10000.0;

returns;
}

这个公式在大多数情况下比较正确，只有在处理球面上的相对点的时候，会出现问题，有一个修正的公式，因为没有需要，就没有找出来，可以在wiki上查到。
当然，我们都知道，地球其实并不是一个真正的圆球体，而是椭球，所以有了下面的公式：

/**
* approx distance between two points on earth ellipsoid
* @param {Object} lat1
* @param {Object} lng1
* @param {Object} lat2
* @param {Object} lng2
*/
functiongetFlatternDistance(lat1,lng1,lat2,lng2){
varf=getRad((lat1+lat2)/2);
varg=getRad((lat1-lat2)/2);
varl=getRad((lng1-lng2)/2);

varsg=Math.sin(g);
varsl=Math.sin(l);
varsf=Math.sin(f);

vars,c,w,r,d,h1,h2;
vara=EARTH_RADIUS;
varfl=1/298.257;

sg=sg*sg;
sl=sl*sl;
sf=sf*sf;

s=sg*(1-sl)+(1-sf)*sl;
c=(1-sg)*(1-sl)+sf*sl;

w=Math.atan(Math.sqrt(s/c));
r=Math.sqrt(s*c)/w;
d=2*w*a;
h1=(3*r-1)/2/c;
h2=(3*r+1)/2/s;

returnd*(1+fl*(h1*sf*(1-sg)-h2*(1-sf)*sg));
}


这个公式计算出的结果要比第一个好一些，当然，最后结果的经度实际上还取决于传入的坐标的精度。













