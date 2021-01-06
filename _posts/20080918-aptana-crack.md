---
title: Aptana的破解
date: 2008-09-18 14:15:01
tag: 
keywords: aptana
description: aptana 是一款基于Eclipse的JS开发IDE。
---

> 工欲善其事、必先利其器

最近写JS比较多，常常苦恼与没有一个顺手的IDE。Editplus虽然用的熟，不过那个的效率太低而且代码看起来也很不方便，经过一个多月的试用，发现了一款好用的编写JS的IDE--Aptana，一个基于Eclipse的JS开发环境。

今天先来说一下破解，因为我是用到试用期快满的时候才想起来破解的。如果不破解，在右下角会有一个剩余多少时间的提示，那怎么破解呢。

关键在 `\Aptana\Aptana Studio\plugin\com.aptana.ide.core_1.1.7.015414.jar` 中，只要修改其中的license类，编译并替换后就可以了。


代码如下：
```java
package com.aptana.ide.core.licensing;

import java.math.BigInteger;
import java.util.Calendar;
import java.util.TimeZone;
import java.util.zip.CRC32;

public final class ClientKey {
    private static class Decrypt {

        private BigInteger modulus;
        private BigInteger exponent;

        public String decrypt(String encrypted) {
            long crc32Value;
            byte bytes[];
            CRC32 crc32;
            if (encrypted == null) {
                encrypted = "";
            } else {
                encrypted = ClientKey.trimEncryptedLicense(encrypted);
            }
            BigInteger big = new BigInteger(encrypted);
            BigInteger decrypted = big.modPow(exponent, modulus);
            crc32Value = (long) decrypted.intValue() & 0xffffffffL;
            decrypted = decrypted.shiftRight(32);
            bytes = decrypted.toByteArray();
            crc32 = new CRC32();
            crc32.update(bytes);
            if (crc32Value == crc32.getValue()) {
                return new String(bytes);
            }
            return null;
        }

        Decrypt(String exponent, String modulus) {
            this.modulus = new BigInteger(modulus);
            this.exponent = new BigInteger(exponent);
        }
    }

    public static final String BEGIN_LICENSE_MARKER = "--begin-aptana-license--";
    public static final String END_LICENSE_MARKER = "--end-aptana-license--";
    private static final TimeZone GMT = TimeZone.getTimeZone("GMT");
    private static final String EMAILS_NON_MATCHING = "EMAILS_NON_MATCHING";
    private static final int PRO = 0;
    private static final int TRIAL = 1;
    private String email;
    private long expiration;
    private int type;

    private ClientKey(int type, String email, long expiration) {
        this.type = type;
        this.email = email;
        this.expiration = expiration;
    }

    public static ClientKey decrypt(String encrypted, String email) {
        String modulus = "115801190261221214754334668902722425936509505416457970789287297728816388753627896293249501578830570324705253515546383166989625001335561947096747210280001245977114030627247212292377290543869343996595819188362915644707269064020812435233012510929338706599216007185654748959001143012936618501934698642942289379979";
        String exponent = "65537";
        if (encrypted != null) {
            encrypted = encrypted.trim();
        }
        Decrypt decrypter = new Decrypt(exponent, modulus);
        return decrypt(decrypter, encrypted, email);
    }

    private static ClientKey decrypt(Decrypt decrypter, String encrypted,
            String email) {
        String value = decrypter.decrypt(encrypted);
        if (value == null) {
            return new ClientKey(1, null, 0L);
        }
        String values[] = value.split(";");
        int type = 1;
        String genedEmail = null;
        long expiration = 0L;
        if (values.length == 3) {
            if ("p".equals(values[0].toLowerCase())) {
                type = 0;
            }
            genedEmail = values[1];
            if (genedEmail != null) {
                if (!genedEmail.equalsIgnoreCase(email)) {
                    genedEmail = "EMAILS_NON_MATCHING";
                }
            } else {
                genedEmail = null;
            }
            try {
                expiration = Long.parseLong(values[2]);
            } catch (Exception _ex) {
                expiration = 0L;
            }
        }
        return new ClientKey(type, genedEmail, expiration);
    }

    public boolean isCloseToExpiring() {
        return false;
    }

    public boolean isValid() {
        return true;
    }

    public boolean isCloseToMatching() {
        return false;
    }

    public boolean isExpired() {
        return false;
    }

    public String getEmail() {
       //把此处的Email修改为你自己的Email地址
        return "heimazhao@gmail.com";
    }

    public Calendar getExpiration() {
        Calendar expirationCal = Calendar.getInstance(GMT);
          //把此处的时间修改为授权到期时间
        expirationCal.set(2010, 9, 9);

        return expirationCal;
    }

    public boolean isTrial() {
        return false;
    }

    public boolean isPro() {
        return true;
    }

    public boolean shouldProPluginsRun() {
        return true;
    }

    public static String trimEncryptedLicense(String encrypted) {
     String newEncrypted = encrypted;
     newEncrypted = newEncrypted.trim();
    newEncrypted = newEncrypted.replaceAll("--begin-aptana-license--", "");
     newEncrypted = newEncrypted.replaceAll("--end-aptana-license--", "");
     newEncrypted = newEncrypted.replaceAll("/s+", "");
    return newEncrypted;
}
}
```

完成后改成名字和原来一样的jar包替换就行了，我是用的版本是com.aptana.ide.core_1.1.7.015414.jar，破解没出现问题。
今后在总结一些使用上的技巧，希望能够在自己手中发挥Aptana的强大威力。













