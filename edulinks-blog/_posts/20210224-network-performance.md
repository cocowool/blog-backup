---
title: 网络性能测试的一般指标与方法
date: 2021-02-23 17:59:59
tags:
keywords: 网络性能测试, 性能测试, netperf
description: 本文总结了网络性能测试的常用指标，并简单介绍了netperf这个用于网络测试的工具，帮助大家了解自己网络的性能。
---

本文介绍度量网络性能的几个指标。

## 网络性能度量的五个指标

- 可用性（availability）
- 响应时间（response time）
- 网络利用率（network utilization）
- 网络吞吐量（network throughput）
- 网络带宽容量（network bandwidth capacity）

### 1．可用性

测试网络性能的第一步是确定网络是否正常工作，最简单的方法是使用 ping 命令。通过向远端的机器发送 icmp echo request，并等待接收 icmp echo reply 来判断远端的机器是否连通，网络是否正常工作。

Ping 命令有非常丰富的命令选项，比如 -c 可以指定发送 echo request 的个数，-s 可以指定每次发送的 ping 包大小。

网络设备内部一般有多个缓冲池，不同的缓冲池使用不同的缓冲区大小，分别用来处理不同大小的分组（packet）。例如交换机中通常具有三种类型的包缓冲：一类针对小的分组，一类针对中等大小的分组，还有一类针对大的分组。为了测试这样的网络设备，测试工具必须要具有发送不同大小分组的能力。Ping 命令的 -s 就可以使用在这种场合。

### 2．响应时间

Ping 命令的 echo request/reply 一次往返所花费时间就是响应时间。有很多因素会影响到响应时间，如网段的负荷，网络主机的负荷，广播风暴，工作不正常的网络设备等等。在网络工作正常时，记录下正常的响应时间。当用户抱怨网络的反应时间慢时，就可以将现在的响应时间与正常的响应时间对比，如果两者差值的波动很大，就能说明网络设备存在故障。

### 3．网络利用率

网络利用率是指网络被使用的时间占总时间（即被使用的时间+空闲的时间）的比例。比如，Ethernet 虽然是共享的，但同时却只能有一个报文在传输。因此在任一时刻，Ethernet 或者是 100% 的利用率，或者是 0% 的利用率。计算一个网段的网络利用率相对比较容易，但是确定一个网络的利用率就比较复杂。因此，网络测试工具一般使用网络吞吐量和网络带宽容量来确定网络中两个节点之间的性能。

### 4．网络吞吐量

网络吞吐量是指在某个时刻，在网络中的两个节点之间，提供给网络应用的剩余带宽。

网络吞吐量可以帮组寻找网络路径中的瓶颈。比如，即使 client 和 server 都被分别连接到各自的 100M Ethernet 上，但是如果这两个 100M 的Ethernet 被 10M 的 Ethernet 连接起来，那么 10M 的 Ethernet 就是网络的瓶颈。

网络吞吐量非常依赖于当前的网络负载情况。因此，为了得到正确的网络吞吐量，最好在不同时间（一天中的不同时刻，或者一周中不同的天）分别进行测试，只有这样才能得到对网络吞吐量的全面认识。

有些网络应用程序在开发过程的测试中能够正常运行，但是到实际的网络环境中却无法正常工作（由于没有足够的网络吞吐量）。这是因为测试只是在空闲的网络环境中，没有考虑到实际的网络环境中还存在着其它的各种网络流量。所以，网络吞吐量定义为剩余带宽是有实际意义的。

### 5．网络带宽容量

与网络吞吐量不同，网络带宽容量指的是在网络的两个节点之间的最大可用带宽。这是由组成网络的设备的能力所决定的。

测试网络带宽容量有两个困难之处：在网络存在其它网络流量的时候，如何得知网络的最大可用带宽；在测试过程中，如何对现有的网络流量不造成影响。网络测试工具一般采用 packet pairs 和 packet trains 技术来克服这样的困难。

## Netperf介绍

Netperf是一种网络性能的测量工具，由惠普公司开发，主要针对基于TCP或UDP的传输。Netperf根据应用的不同，可以进行不同模式的网络性能测试，即批量数据传输（bulk data transfer）模式和请求/应答（request/reponse）模式。Netperf测试结果所反映的是一个系统能够以多快的速度向另外一个系统发送数据，以及另外一个系统能够以多块的速度接收数据。

Netperf工具以client/server方式工作。server端是netserver，用来侦听来自client端的连接，client端是netperf，用来向server发起网络测试。在client与server之间，首先建立一个控制连接，传递有关测试配置的信息，以及测试的结果；在控制连接建立并传递了测试配置信息以后，client与server之间会再建立一个测试连接，用来来回传递着特殊的流量模式，以测试网络的性能。

除了netperf以外，还有很多其它的网络性能测试工具，如dbs, iperf, pathrate, nettest, netlogger, tcptrace, ntop等。这些工具有其各自的特色和不同的侧重点，我们可以根据具体的应用环境，有选择的使用它们，这样就可以使这些工具发挥出最大的功效。虽然都是开放源代码的软件，但是这些工具的功能与商业的网络测试工具同样强大，而且也得到了广泛的应用，熟悉这些工具对我们的实际工作一定会有很大的帮助。