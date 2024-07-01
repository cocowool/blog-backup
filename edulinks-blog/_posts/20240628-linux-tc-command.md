---
title: 20240628-linux-tc-command
date: 2024-06-28 09:50:06
keywords:
description:
---


使用 tc 命令模拟客户端到服务端请求之间网络服务不稳定的场景

一、引言

在软件开发和网络测试过程中，模拟各种网络状况是非常有用的。它可以帮助我们验证应用程序在不同网络环境下的稳定性和性能。Linux中的tc命令（Traffic Control）就是这样一个强大的工具，它可以用来模拟各种网络条件，如带宽限制、延迟、丢包等。本文将详细介绍如何使用tc命令来模拟客户端到服务端请求之间网络服务不稳定的场景，并通过具体的代码示例来展示其用法。

二、tc 命令简介

tc命令是Linux操作系统中用于配置网络流量控制规则的工具。它基于Linux内核的队列规则（qdisc）和过滤器（filter）来实现流量控制。通过tc命令，我们可以对经过网络接口的数据包进行各种处理，从而模拟出不同的网络状况。

三、使用 tc 命令模拟网络服务不稳定场景

（一）模拟带宽限制

假设我们想要模拟一个带宽受限的网络环境，其中客户端到服务端的连接速度被限制为1Mbps。我们可以使用tc命令中的qdisc和filter来实现。

为网络接口配置qdisc
首先，我们需要为网络接口（假设为eth0）配置一个qdisc。这里我们使用root_handle为1:的qdisc，并指定使用htb（层次化令牌桶）算法。

bash
sudo tc qdisc add dev eth0 root handle 1: htb
创建类并限制带宽
接下来，我们需要在qdisc下创建一个类，并指定该类的带宽限制。这里我们创建一个名为limited_bw的类，并限制其带宽为1Mbps。

bash
sudo tc class add dev eth0 parent 1: classid 1:1 htb rate 1mbit
配置过滤器以应用带宽限制
最后，我们需要配置一个过滤器来将特定的数据流（如从客户端到服务端的请求）匹配到上述类中。这里我们使用IP协议和源/目标IP地址来匹配数据流。

bash
sudo tc filter add dev eth0 protocol ip parent 1:0 prio 1 u32 match ip src CLIENT_IP match ip dst SERVER_IP flowid 1:1
注意：将CLIENT_IP和SERVER_IP替换为实际的客户端和服务端IP地址。

（二）模拟延迟和丢包

除了带宽限制外，我们还可以使用tc命令来模拟延迟和丢包。这可以通过在qdisc下创建不同的类和过滤器来实现。

模拟延迟
要在数据流中添加延迟，我们可以使用netem qdisc（网络模拟器）并指定delay参数。以下示例将延迟设置为100ms：

bash
sudo tc qdisc add dev eth0 root netem delay 100ms
注意：这将会影响通过eth0接口的所有数据流。如果你只想影响特定的数据流，可以像上面那样使用过滤器来匹配数据流。

模拟丢包
要模拟丢包，我们可以在netem qdisc中指定loss参数。以下示例将丢包率设置为10%：

bash
sudo tc qdisc add dev eth0 root netem loss 10%
同样地，你也可以使用过滤器来将丢包规则应用于特定的数据流。

（三）代码示例

下面是一个简单的Python示例，用于演示如何在使用tc命令模拟的网络不稳定环境中发送HTTP请求。请注意，这个示例仅用于演示目的，并不包含完整的错误处理和重试逻辑。

```python
import requests  
import time  
  
def send_request():  
    url = 'http://SERVER_IP:PORT/api/endpoint'  # 替换为实际的服务端URL和端口  
    try:  
        response = requests.get(url, timeout=5)  
        print(f"Received response: {response.status_code}")  
    except requests.exceptions.RequestException as e:  
        print(f"Request failed: {e}")  
  
# 模拟不稳定网络环境（在发送请求之前运行相应的tc命令）  
# ...  
  
# 发送请求并观察结果  
for i in range(10):  
    send_request()  
    time.sleep(1)  # 每秒发送一个请求  
  
# 清理tc规则（在测试完成后运行）  
# sudo tc qdisc del dev eth0 root
```

在这个示例中，我们首先使用tc命令模拟了一个不稳定的网络环境（如带宽限制、延迟或丢包）。然后，我们使用Python的requests库来发送HTTP请求，并观察请求的结果。最后，我们清理了之前设置的tc规则，以便恢复正常的网络环境。

## 参考资料
1. 