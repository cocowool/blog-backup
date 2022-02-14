---
title: macOS Monterey 无线网卡驱动
date: 2022-02-14 21:31:03
keywords: ComFast 驱动, macOS Monterey 无线驱动, ComFast CF-811AC, ComFast CF-WU810N
description: 因为工作环境的原因，我的MBP需要安装两块网卡。假期手残将系统从 Catalina 升级到了 Monterey，结果驱动不能用了，本文记录一天折腾驱动升级的过程。
---

工作主力使用MBP 16 2020年款，最后一代Intel的芯片，一直使用 macOS Catalina 的操作系统。

因为环境需求要两块网卡，除了笔记本自带的网卡，还一直使用 COMFAST 的无线网卡。小巧方便，价格便宜，确实推荐。

假期手残，将 Catalina 升级到了 Monterey，这下悲剧了，无线网卡驱动无法使用，不能识别出网卡。

先通过百度搜索了一些资料，大部分都是 10.15 相关版本的安装教程。

印象中上一次折腾驱动时，COMFAST 好像使用的是 RealTK 的芯片，再通过 Bing.com 搜索相关的关键词，找到了最关键的一篇文章 [github.com/chris1111 - Wireless-USB-Big-Sure-Adapter](https://github.com/chris1111/Wireless-USB-Big-Sur-Adapter) 。

这里面列出了驱动支持的设备列表：

- Alfa AWUS036AC
- Alfa AWUS036ACH
- Archer T2U NANO, MINI, AC600
- Archer T3U
- Archer T3U Plus
- Archer T2U MINI V3
- Archer T2U Plus
- ArcherT4U V1, V2, V3
- Archer T9UH V2
- ASUS USB AC68
- ASUS USB-N13
- COMFAST CF-811AC
- COMFAST CF-812AC
- Cudy WU1300S
- Cudy WU700
- DLink DWA-121 N150
- EDIMAX EW-7611UCB
- EDIMAX EW-7722UTn V2
- EDIMAX_EW-7822ULC
- EDIMAX EW-7612Uan V2
- EDIMAX EW-7833UAC
- EDIMAX N300
- EDIMAX EW-7811Un (N150)
- EDUP EP-AC1689
- Fenvi AC1300 (RTL8812bu)
- Linksys WUSB6300 V2
- Linksys WUSB6400M
- Netgear A7000
- Plexgear AC1200
- Sitecom WLA7100
- TL-WN823Nv2/v3
- TL-WN725Nv3
- TL-WN723Nv2/v3
- TL-WN722Nv2/v3
- TL-WN821Nv6
- TL-WN822Nv4/v5
- TENDA W311-MINI
- TRENDnet N150 Micro
- TRENDnet TEW-808UBM
- TRENDnet TEW-908UB
- YUNCLOUD Realtek (RTL8814AU)
- ZAPO W58L (RTL881lAU)

我使用的网卡也在其中，这就有希望了。

下载了 Wireless USB OC Big Sur Adapter-V12 和 Wireless USB Clover Big Sur Adapter-V9 反复安装重启了几次，都没有成功。当时简直有些绝望了，甚至还在 Github 上给作者开了一个 issue。没想到作者还非常活跃，不到两个小时，就给我回复了一条消息。

后来想是不是老版的驱动还可以使用，尝试下安装 「RTLWlanU_MacOS10.11_MacOS10.15_Driver_1830.32.b17_1827.4.b36_UI_5.0.9.b9」这个软件，最后安装失败了。菜单栏中的 WiFi 图标也不见了。

最后尝试安装 「Wireless USB OC Big Sur Adapter-V12」重启后，没想到居然可以使用了。

现在回想，可能安装步骤是这样

* 首先需要安装 RealTK 的驱动程序
* 然后再安装 OC 配置程序，chris1111 提供的是配置程序，不包含驱动

不知道我的理解对不对，欢迎大家和我交流讨论。

## 参考资料

1. [chris1111 - Wireless-USB-Big-Sure-Adapter](https://github.com/chris1111/Wireless-USB-Big-Sur-Adapter)
2. [How to Disable SIP Security #115](https://github.com/chris1111/Wireless-USB-Big-Sur-Adapter/discussions/115)
3. [Drivers for Realtek 802.11n and 802.11ac USB Wi-Fi adapters](https://www.insanelymac.com/forum/topic/349973-drivers-for-realtek-80211n-and-80211ac-usb-wi-fi-adapters/)
4. [USB Wireless Adapters for Catalina, Big Sur and Monterey](https://www.tonymacx86.com/threads/usb-wireless-adapters-for-catalina-big-sur-and-monterey.318109/)
5. [macOS Big Sur黑苹果USB万能无线网卡驱动下载 安装教程](https://zhuanlan.zhihu.com/p/405463458?ivk_sa=1024320u)
6. [黑苹果10.15.7安装comfast永存，CF-811AC驱动方法](https://blog.csdn.net/qq_34100267/article/details/113728926)
