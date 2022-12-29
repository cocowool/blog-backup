---
title: 编写脚本利用Ping命令探测网络质量
date: 2021-06-02 16:09:22
tags:
keywords: ping, linux ping
description: 本文介绍在脚本中使用ping命令判断主机是否可达以及响应时间。
---

Ping 命令是我们用来检查网络通断性和网络稳定性的常用工具。

如果只需要获取Ping的结果，可以用 `$?` 获取执行结果即可。注意在脚本中使用 `ping` 命令时，一定要使用 `-c` 参数指定次数。

```sh
#!/bin/sh
 
# -q quiet
# -c nb of pings to perform
 
ping -q -c5 google.com > /dev/null
 
if [ $? -eq 0 ]
then
	echo "ok"
fi
```

如果想要获得并展示 `ping` 的结果，因为Ping的结果没有办法管道给其他命令直接处理，所以先将Ping的结果存放到变量或者临时文件中，然后再进行后续的处理。在输出的过程中使用了printf进行输出结果的格式化。

```sh
#!/bin/sh
 
# Find the fastest apple store hosts
 
printf "%-20s\t%-16s\t%-10s\n" "Domain" "Ip" "Avg Ping"
#printf "Domain\tIp\tAvg Ping\n"
for ((I=1 ;I < 2001; I++ ));do
    HOST="a$I.phobos.apple.com"
    TEMP=$(ping -c 4 $HOST)
    IP=$(echo $TEMP | grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' | uniq)
    TIME=$(echo $TEMP | grep -oE '\/([0-9]*\.[0-9]*)\/' | grep -oE '[0-9]*\.[0-9]*')
    printf "%-20s\t%-16s\t%-10s\n" $HOST $IP $TIME
done
```

