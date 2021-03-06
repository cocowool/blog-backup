---
title: 关于如何做好运维管理工作的一点思考
date: 2020-10-10 19:57:20
tags:
keywords: 运维管理, 生产故障, 安全稳定, 手段
description: 本文中所说的运维是指应用系统的运维工作，与传统的Linux运维、数据库运维不同，应用系统运维更多的是从一个线上的业务系统能否安全稳定运行的角度来思考，包括系统日常的运行状况是不是正常、遇到线上生产故障能不能快速恢复、对于突发事件有没有对应的处置手段等等，总的目的只有一个，就是要想尽办法保障不管在什么情况下，都有措施或手段能够快速的恢复业务的运行。
---


9月底的时候，我们团队负责的两个系统在几周内连续发生了两次线上的生产故障，虽然最后并没有发生严重的损失，但是领导免不了要提一些更高的要求，围绕 `保持安全稳定，避免故障再次发生` 这个目标需要梳理种种可能的优化措施，也借此机会来梳理下我对于如何做好运维管理工作的一些看法，欢迎各位同行批评指正。

本文中所说的运维是指应用系统的运维工作，与传统的Linux运维、数据库运维不同，应用系统运维更多的是从一个线上的业务系统能否安全稳定运行的角度来思考，包括系统日常的运行状况是不是正常、遇到线上生产故障能不能快速恢复、对于突发事件有没有对应的处置手段等等，总的目的只有一个，就是要想尽办法保障不管在什么情况下，都有措施或手段能够快速的恢复业务的运行。当然具体到不同业务系统，可以根据业务时效性要求和重要性要求划分不同的保障等级，本文略过不表。

应用系统运维与面向单个产品的运维相比，有以下两个特点：

* **运维对象较多且可能比较复杂**。既有基础的操作系统、数据库，也有自主开发的应用程序，可能还会涉及很多的开源软件如Kafka、Zookeeper、Redis、MongoDB等等。
* **运维过程中打交道的人也比较多**。操作系统管理员、数据库管理员、各种监控工具管理员、项目开发人员、业务部门人员等等。

这两个特点决定了应用系统运维管理人员没有办法精通所有的领域，如何实现运维的目标更多的要靠管理手段而不是技术能力，管理手段也大体上分为三个层次：

* **尽早发现问题的手段**。
* **快速恢复业务的手段**。
* **紧急处置故障的手段**。

我认为这三个层次的重要性依次降低，如果我们做好了前面的功课，可能会降低后面事情发生的概率，下面依次展开说一下每个层次的具体手段。

## 尽早发现问题的手段

* **监控**。这个的重要性自不必说，全面的监控指标和灵敏的阈值能够让我们收到很多告警短信，但最重要的是我们要能发现哪一条才是会影响业务的。
* **日常巡检**。巡检尽量做成自动化的方式，但是巡检报告的解读必须运维管理人员亲自操刀，尽量做到对每一个异常项都刨根问底。巡检即包括操作系统的检查，例如磁盘空间、文件句柄等，也包括数据库的检查，例如AWR报告、慢查询等，还应该包括业务系统的检查，包括营业日历是否正确、系统在线人数有没有破新高等等。
* **值班制度**。不管是不是现场，我觉得坚持值班制度非常有必要，通过值班安排落实了职责，避免三个和尚没水喝的尴尬情况。

## 快速恢复业务的手段

* **流量隔离**。这个手段我觉得是最好的恢复业务的手段，但是所需要的投入往往非常大，首先需要应用是集群部署，这样才能支持流量的调度和隔离，其次还要给运维人员提供隔离的工具或手段，如果运维人员在开发阶段能够提出这个要求，后续运维过程中可以节省不少人力。
* **重启应用**。这个的前提是要确保你的应用程序是对状态不敏感或者支持优雅重启的，这个办法往往能解决80%的生产问题。
* **重启操作系统**。如果重启应用还不能解决问题，那么就重启操作系统好了。
* **主备切换**。对于主备模式部署的应用，这往往是最后考虑的一个手段，而且一般还不敢尝试，特别是在有数据同步的场景下。

在执行快速恢复业务手段之前，我们都应该牢记把故障设备的现场信息收集记录下来，为开发人员排查、再现问题提供重要的现场数据。

## 紧急处置故障的手段

如果前两个部分的手段都不能帮助解决生产问题，到这个层次需要有提前的准备才行，例如日常的备份、异地的备份等等，如果日常的备份也没有，那还有一个终极的办法，那就是 `拉开发来上紧急版本啊` 。

