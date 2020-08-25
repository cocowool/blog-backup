---
title: Aptana的破解
date: 2008-09-18 14:15:01
tag: 
---

---工欲善其事、必先利其器

最近写JS比较多，常常苦恼与没有一个顺手的IDE。Editplus虽然用的熟，不过那个的效率太低而且代码看起来也很不方便，经过一个多月的试用，发现了一款好用的编写JS的IDE--Aptana，一个基于Eclipse的JS开发环境。

今天先来说一下破解，因为我是用到试用期快满的时候才想起来破解的。如果不破解，在右下角会有一个剩余多少时间的提示，那怎么破解呢。

关键在\Aptana\Aptana Studio\plugin\com.aptana.ide.core_1.1.7.015414.jar中，只要修改其中的license类，编译并替换后就可以了。


代码如下：
packagecom.aptana.ide.core.licensing;

importjava.math.BigInteger;
importjava.util.Calendar;
importjava.util.TimeZone;
importjava.util.zip.CRC32;

publicfinalclassClientKey {
privatestaticclassDecrypt {

privateBigInteger modulus;
privateBigInteger exponent;

publicString decrypt(String encrypted) {
longcrc32Value;
bytebytes[];
CRC32 crc32;
if(encrypted==null) {
encrypted="";
}else{
encrypted=ClientKey.trimEncryptedLicense(encrypted);
}
BigInteger big=newBigInteger(encrypted);
BigInteger decrypted=big.modPow(exponent, modulus);
crc32Value=(long) decrypted.intValue()&0xffffffffL;
decrypted=decrypted.shiftRight(32);
bytes=decrypted.toByteArray();
crc32=newCRC32();
crc32.update(bytes);
if(crc32Value==crc32.getValue()) {
returnnewString(bytes);
}
returnnull;
}

Decrypt(String exponent, String modulus) {
this.modulus=newBigInteger(modulus);
this.exponent=newBigInteger(exponent);
}
}

publicstaticfinalString BEGIN_LICENSE_MARKER="--begin-aptana-license--";
publicstaticfinalString END_LICENSE_MARKER="--end-aptana-license--";
privatestaticfinalTimeZone GMT=TimeZone.getTimeZone("GMT");
privatestaticfinalString EMAILS_NON_MATCHING="EMAILS_NON_MATCHING";
privatestaticfinalintPRO=0;
privatestaticfinalintTRIAL=1;
privateString email;
privatelongexpiration;
privateinttype;

privateClientKey(inttype, String email,longexpiration) {
this.type=type;
this.email=email;
this.expiration=expiration;
}

publicstaticClientKey decrypt(String encrypted, String email) {
String modulus="115801190261221214754334668902722425936509505416457970789287297728816388753627896293249501578830570324705253515546383166989625001335561947096747210280001245977114030627247212292377290543869343996595819188362915644707269064020812435233012510929338706599216007185654748959001143012936618501934698642942289379979";
String exponent="65537";
if(encrypted!=null) {
encrypted=encrypted.trim();
}
Decrypt decrypter=newDecrypt(exponent, modulus);
returndecrypt(decrypter, encrypted, email);
}

privatestaticClientKey decrypt(Decrypt decrypter, String encrypted,
String email) {
String value=decrypter.decrypt(encrypted);
if(value==null) {
returnnewClientKey(1,null,0L);
}
String values[]=value.split(";");
inttype=1;
String genedEmail=null;
longexpiration=0L;
if(values.length==3) {
if("p".equals(values[0].toLowerCase())) {
type=0;
}
genedEmail=values[1];
if(genedEmail!=null) {
if(!genedEmail.equalsIgnoreCase(email)) {
genedEmail="EMAILS_NON_MATCHING";
}
}else{
genedEmail=null;
}
try{
expiration=Long.parseLong(values[2]);
}catch(Exception _ex) {
expiration=0L;
}
}
returnnewClientKey(type, genedEmail, expiration);
}

publicbooleanisCloseToExpiring() {
returnfalse;
}

publicbooleanisValid() {
returntrue;
}

publicbooleanisCloseToMatching() {
returnfalse;
}

publicbooleanisExpired() {
returnfalse;
}

publicString getEmail() {
//把此处的Email修改为你自己的Email地址
return"heimazhao@gmail.com";
}

publicCalendar getExpiration() {
Calendar expirationCal=Calendar.getInstance(GMT);
//把此处的时间修改为授权到期时间
expirationCal.set(2010,9,9);

returnexpirationCal;
}

publicbooleanisTrial() {
returnfalse;
}

publicbooleanisPro() {
returntrue;
}

publicbooleanshouldProPluginsRun() {
returntrue;
}

publicstaticString trimEncryptedLicense(String encrypted) {
String newEncrypted=encrypted;
newEncrypted=newEncrypted.trim();
newEncrypted=newEncrypted.replaceAll("--begin-aptana-license--","");
newEncrypted=newEncrypted.replaceAll("--end-aptana-license--","");
newEncrypted=newEncrypted.replaceAll("/s+","");
returnnewEncrypted;
}
}

完成后改成名字和原来一样的jar包替换就行了，我是用的版本是com.aptana.ide.core_1.1.7.015414.jar，破解没出现问题。
今后在总结一些使用上的技巧，希望能够在自己手中发挥Aptana的强大威力。













