---
title: Python 与 ActiveMQ 交互的一些例子
date: 2022-08-25 19:56:15
keywords:
description:
---

> 本文基于 ActiveMQ 5.8.0 以及 Python 3.9.13
>
> ActiveMQ [官方网站](https://activemq.apache.org/) 中显示，ActiveMQ 已经分成 ActiveMQ Classic 和 ActiveMQ Artemis 两个分支，其中 Classic 主要是 5.x 的版本，Artemis 是 6.x 的版本。

网上搜到的文章大多是用一段代码生产和消费数据，和实际应用有一些区别，因此专门整理了几段代码，更贴合生产实际的需要。如果需要 ActiveMQ 的本地环境，可以参考 [构建ActiveMQ镜像并运行](http://www.edulinks.cn/2021/10/21/20211021-make-activemq-docker-image/) 快速搭建本地环境。

编写几个 Python 操作 ActiveMQ 的例子，演示 Python 使用 STOMP 协议连接 ActiveMQ 的场景。

* 向队列中发送消息
* 消费队列中的数据
* 向 Topic 中发送消息
* 消费 Topic 中的消息

## 向队列中发送消息

```python
import time
import sys

import stomp

class MyListener(stomp.ConnectionListener):
    def on_error(self, frame):
        print('received an error "%s"' % frame.body)

    def on_message(self, frame):
        print('received a message "%s"' % frame.body)

conn = stomp.Connection([('localhost',61613)])
conn.set_listener('logicServerQueue', MyListener())
# conn.start()
conn.connect('admin', 'admin', wait=True)
# conn.subscribe(destination='/queue/test_queue', id=1, ack='auto')

while True:
    t=time.gmtime()
    msg=" hello  " + time.strftime("%Y-%m-%d %H:%M:%S",t)
    conn.send(body=msg, destination='/queue/test', headers={'consumerId': 'qmsg_producer'})
    print(" send : " + msg)
    time.sleep(10)

conn.disconnect()
```

## 消费队列中的数据

```python
import time
import sys

import stomp

class MyListener(stomp.ConnectionListener):
    def on_error(self, frame):
        print('received an error "%s"' % frame.body)

    def on_message(self, frame):
        print('received a message "%s"' % frame.body)

conn = stomp.Connection()
conn.set_listener('', MyListener())
conn.connect('admin', 'admin', wait=True)
conn.subscribe(destination='/queue/test', id=1, ack='auto')

while True:
    time.sleep(5)
```

将消费者放入了循环中，否则消费一次后会退出整个应用。

## 向 Topic 中发送消息

```python
import time
import sys

import stomp

class MyListener(stomp.ConnectionListener):
    def on_error(self, frame):
        print('received an error "%s"' % frame.body)

    def on_message(self, frame):
        print('received a message "%s"' % frame.body)

conn = stomp.Connection([('localhost',61613)])
conn.set_listener('logicServerQueue', MyListener())
# conn.start()
conn.connect('admin', 'admin', wait=True)
# conn.subscribe(destination='/queue/test_queue', id=1, ack='auto')

while True:
    t=time.gmtime()
    msg=" hello  " + time.strftime("%Y-%m-%d %H:%M:%S",t)
    conn.send(body=msg, destination='/topic/test', headers={'consumerId': 'topic_producer'})
    print(" send : " + msg)
    time.sleep(10)

conn.disconnect()
```

## 消费 Topic 中的消息

```python
import time
import sys

import stomp

class MyListener(stomp.ConnectionListener):
    def on_error(self, frame):
        print('received an error "%s"' % frame.body)

    def on_message(self, frame):
        print('received a message "%s"' % frame.body)

conn = stomp.Connection()
conn.set_listener('', MyListener())
conn.connect('admin', 'admin', wait=True)
conn.subscribe(destination='/queue/test', id=1, ack='auto')

while True:
    time.sleep(5)
```

## 参考资料

1. [python stomp 收发指定的消息](https://blog.csdn.net/benhuo931115/article/details/77030869)
1. [ActiveMQ 配置自动清除数据](https://blog.csdn.net/vtopqx/article/details/51788065)
