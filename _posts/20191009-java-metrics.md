---
title: Java Metrics工具介绍
date: 2019-10-09 20:26:48
tag: 
keywords: java, java metrics, java linux
description： 本文介绍如何通过Metric来帮助我们对应用程序的性能进行度量，这个包能够帮我省去很多统计方面的工作量。
---

> 本文介绍如何通过Metric来帮助我们对应用程序的性能进行度量，这个包能够帮我省去很多统计方面的工作量。

<!-- more -->

目录

* [简介](#简介)
* [快速入门](#快速入门)
  * [Maven配置](#maven配置)
  * [MetricRegistry](#metricregistry)

* [Gauge](#gauge)
* [Meter](#meter)
* [Counter](#counter)
* [Histgram](#histgram)
* [Timer](#timer)
* [Reporter](#reporter)
* [更多用法](#更多用法)
* [参考资料](#参考资料)



## 简介
Metric是一个第三方包，用来帮助我们对应用程序的性能进行度量。曾有友商基于这个包编写的程序还申请了专利，总之这是一个使用方便的组件。我们日常进行应用程序性能度量时，最常用的方法是打日志记录每个交易的一些耗时数据，有了这些原始数据，自己再进行统计分析。通过使用Metrics这个包，我们可以很方便的定义一些度量值，抓取一些关键时点和变量的信息，还能按照自定义的周期进行总体的统计，来分析应用的性能。Metrics还能够将这些统计信息输出到Console、日志文件、JMX，甚至还支持以微服务的方式向外暴露数据接口，从而方便我们将这些数据接入到Grafana或自己的统计分析工具中。

## 快速入门

### Maven配置
```xml
<dependency>
    <groupId>io.dropwizard.metrics</groupId>
    <artifactId>metrics-core</artifactId>
    <version>4.0.5</version>
</dependency>
```
> 本文发表时，最新版本是4.1.0


### MetricRegistry
使用MetricRegistry注册一个或多个metrics。如果需要多个报告，则需要声明多个MetricRegistry对象。
有两种注册方式

```java
MetricRegistry metricRegistry = new MetricRegistry();

Meter meter1 = new Meter();
metricRegistry.register("meter1", meter1);
 
Meter meter2 = metricRegistry.meter("meter2");
```
> 更为详细的代码示例可以参考[我的Git](https://github.com/cocowool/sh-valley)

## Gauge

Gauge能做的就是返回一个变量的瞬时值，在此基础上还提供了```RatioGauge\CachedGauge\DerivativeGauge\JmxAttributeGauge```的使用。

## Meter
Meter用来度量事件并发的数量和速度。

## Counter
Counter度量类型是一种特殊的Gauge度量，因为其持有的值就是一个AtomicLong，可以递增也可以递减。

## Histgram
Histogram度量类型用于测量一个数据流各值的统计分布。其除了能够测量最大值、最小值、平均值外，还可以测量中位数、75、90、95、98、99和99.9%等。
Histogram支持四种数据采样方式```ExponentiallyDecayingReservior, UniformReservoir, SlidingTimeWindowReservior, SlidingWindowReservior```。

## Timer
Timer度量类型包含了Meter和Histogram的统计，即比率和统计信息的综合。

## Reporter
使用Reporter可以输出测量结果，支持```ConsoleReporter, CsvReporter, Slf4Reporter, JmxReporter```等等。

## 更多用法
通过```metrics-healthchecks```和```metrics-servlets```模块，还能支持健康检查和微服务的数据输出，有兴趣的朋友可以继续探索。

## 参考资料

1. [性能分析之Java Metrics度量包](https://cloud.tencent.com/developer/article/1465669)
2. [Java Code Examples for com.yammer.metrics.core.Gauge](https://www.programcreek.com/java-api-examples/index.php?api=com.yammer.metrics.core.Gauge)
3. [Metrics Getting Started](https://metrics.dropwizard.io/4.1.0/getting-started.html)
4. [[Metrics扫盲]---(1)如何在java应用中使用Metrics](https://blog.csdn.net/zhaoyaxuan001/article/details/86659604)
5. [Intro to Dropwizard Metrics](https://www.baeldung.com/dropwizard-metrics)












