---
title: 短链接算法收集与分析
date: 2011-12-29 13:10:01
tag: 
---

短链接就不说了，大家已经都清楚了，如下所示就是短链接：
> 新浪微博[http://t.cn/SVpONM](http://t.cn/SVpONM)
腾讯微博[http://url.cn/302yor](http://url.cn/302yor?type=1&from=19&u=wonwang&s=3&f=1&skey=)
Yun.io[http://d.yun.io/PNri2v](http://d.yun.io/PNri2v)

短链接的好处：1、内容需要；2、用户友好；3、便于管理。
如何实现呢，大概有三个步骤：
1、定义一个URL映射算法，可以将长的URL映射成短字符串；
2、使用一个存储（数据库？NoSQL？）来存储完成的映射；
3、实现自己的URL映射算法；
一般来说，第三步是我们比较头疼的，如何将一个长的URL字符串，映射成一个较短的字符串呢。我总结了三种办法：
**普通实现**
我想以前大家学习过十进制和二进制的互相转换，或者十进制和十六进制的互相转换，那么为了更短，我们可以使用62进制，对于一个数字ID进行转码，转换成一个短字符串。
这种做法的缺点是没有办法保证所有链接都是固定的位数的长度，而且在高并发的情况下，如何保证能够快速分发是个问题。
具体实现方法：
> /**
* 利用62进制对数字ID进行短链接编码，缺点不能保证每个短链接是固定长度
*
* @author  wanshiqiang<wangshiqiang@360.cn>
* @param integer $integer
* @param string $base
*/
private function getShortenedURLFromID ($integer, $base = ALLOWED_CHARS)
{
$length = strlen($base);
while($integer > $length - 1)
{
$out = $base[fmod($integer, $length)] . $out;
$integer = floor( $integer / $length );
}
return $base[$integer] . $out;
}
/**
* 对62进制编码的短链接进行解码
*
* @author  wangshiqiang<wangshiqiang@360.cn>
* @param string $string
* @param string $base
*/
private function getIDFromShortenedURL ($string, $base = ALLOWED_CHARS)
{
$length = strlen($base);
$size = strlen($string) - 1;
$string = str_split($string);
$out = strpos($base, array_pop($string));
foreach($string as $i => $char)
{
$out += strpos($base, $char) * pow($length, $size - $i);
}
return $out;
}

> 
**文艺实现**
算法描述：使用6个字符来表示短链接，我们使用ASCII字符中的'a'-'z','0'-'5'，共计32个字符做为集合。每个字符有32种状态，六个字符就可以表示32^6（1073741824），那么如何得到这六个字符，描述如下：
对传入的长URL进行Md5，得到一个32位的字符串，这个字符串变化很多，是16的32次方，基本上可以保证唯一性。将这32位分成四份，每一份8个字符，这时机率变成了16的8次方，是4294967296，这个数字碰撞的机率也比较小啦，关键是后面的一次处理。我们将这个8位的字符认为是16进制整数，也就是1*('0x'.$val)，然后取0-30位，每5个一组，算出他的整数值，然后映射到我们准备的32个字符中，最后就能够得到一个6位的短链接地址。
PHP实现如下：
> function shorten( $long_url )
{
$base32 = "abcdefghijklmnopqrstuvwxyz012345";
$hex = md5( $long_url );
$hexLen = strlen( $hex );
$subHexLen = $hexLen / 8;
$output = array();
for( $i = 0; $i < $subHexLen; $i++ )
{
$subHex = substr( $hex, $i * 8, 8 );
$subHex = 0x3FFFFFFF & ( 1 * ('0x' . $subHex ) );
$out = '';
for( $j = 0; $j < 6; $j++ )
{
$val = 0x0000001F & $int;
$out .= $base32[$val];
$int = $int >> 5;
}
$output[] = $out;
}
return $output;
}

**二逼实现**
下面这个函数使用了纯随机的方式来生成一个短链接，虽然我们可以通过查询操作来确保不重复使用短链接，可是... 这样真的靠谱吗~~
> function random($length, $pool = '') {
$random = '';
if (empty($pool)) { $pool    = 'abcdefghkmnpqrstuvwxyz'; $pool   .=
'23456789'; }
srand ((double)microtime()*1000000);
for($i = 0; $i < $length; $i++) { $random .=
substr($pool,(rand()%(strlen ($pool))), 1); }
return $random;
}

Technorati 标签:[短链接](http://technorati.com/tags/%e7%9f%ad%e9%93%be%e6%8e%a5),[Short Url](http://technorati.com/tags/Short+Url),[映射](http://technorati.com/tags/%e6%98%a0%e5%b0%84),[哈希](http://technorati.com/tags/%e5%93%88%e5%b8%8c)
参考资料：
1、[微博短地址原理解析](http://qn-lf.iteye.com/blog/1084516)
2、[微博短域名原理及作用](http://www.iteye.com/topic/973365?page=2)
3、[Yours.org](http://code.google.com/p/yourls/downloads/list)
4、[Free PHP URL Shorten script that kicks ass](http://briancray.com/2009/08/26/free-php-url-shortener-script/)
5、[PHP Short Url Algorithm Implementation](http://www.snippetit.com/2009/04/php-short-url-algorithm-implementation/)
6、[Implement your own short URL](http://www.snippetit.com/2008/10/implement-your-own-short-url/)
7、[短网址算法初步汇总](http://hi.baidu.com/jok607/blog/item/a2258b25ce28827a35a80f6b.html)
8、[Short Url 实现方式](http://i.laoer.com/short-url.html)












