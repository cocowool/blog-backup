---
title: Redis 基础介绍
date: 2021-08-21 22:40:18
tags:
keywords: redis
description: Redis 是一个内存型数据库，相较于它的前辈 Memcached ，它支持的存储类型比 Memcached 更多，包括字符串（ String ）、链表（ List ）、集合（ Set ）、有序集合（ Zset ）和哈希（ Hash ）。这些数据类型都支持 ` push / pop ` 和 ` add / remove ` 及取交集、并集、差集以及更加丰富的操作，而且这些操作都是原子性的。Redis会周期性把更新的数据写入磁盘，并在此基础上实现了主从同步。
---
## Redis介绍
Redis是一个内存型数据库，是一个高性能的 Key / Value 数据库，相较于它的前辈 Memcached，它支持的存储类型比Memcached更多，包括字符串（String）、链表（List）、集合（Set）、有序集合（Zset）和哈希（Hash）。这些数据类型都支持 `push/pop` 和 `add/remove` 及取交集、并集、差集以及更加丰富的操作，而且这些操作都是原子性的。Redis会周期性把更新的数据写入磁盘，并在此基础上实现了主从同步。

从2010年3月5日开始，Redis的开发工作由VMWare主持。
从2013年5月开始，Redis的开发由Pivotal赞助。

## Redis快速体验
快速体验Redis的最佳方式，是使用Docker。如果想搭建Redis集群，可以参考我的另外一篇文章「[基于Docker快速搭建Redis集群](http://www.edulinks.cn/2021/02/27/20210301-docker-redis/)」
```sh
$ docker pull redis
Using default tag: latest
latest: Pulling from library/redis
27833a3ba0a5: Pull complete 
cde8019a4b43: Pull complete 
97a473b37fb2: Pull complete 
c6fe0dfbb7e3: Pull complete 
39c8f5ba1240: Pull complete 
cfbdd870cf75: Pull complete 
Digest: sha256:000339fb57e0ddf2d48d72f3341e47a8ca3b1beae9bdcb25a96323095b72a79b
Status: Downloaded newer image for redis:latest
$ docker run -p 6379:6379 -d redis:latest redis-server
$ telnet 127.0.0.1 6379
exists xxx
:0
type xxx
+none
del xxx
:0
set xxx hello-redis
+OK
exists xxx
:1
type xxx
+string
$ docker exec -ti d0b86 redis-cli -h 127.0.0.1 -p 6379  //使用redis-cli连接
```

## Redis 支持的数据类型
* String，最基本的数据类型，一个键对应一个值，需要注意是一个键值最大存储512MB。
* Hash，是一个键值对的集合，是一个string类型的field和value的映射表，适合用于存储对象。
* List，简单的字符串列表，它按插入顺序排序。
* Set，是字符串类型的无序集合，也不可重复。
* Zset，是string类型的有序集合，也不可重复。有序集合中的每个元素都需要指定一个分数，根据分数对元素进行升序排序，如果多个元素有相同的分数，则以字典序进行升序排序，sorted set因此非常适合实现排名。

### Redis常用命令


#### String相关命令
| 命令 | 说明 | 备注 |
| --- | --- | --- |
| set key value | 设置key对应的值为string类型的value,返回1表示成功，0失败 |  |
| setnx key value | 如果key不存在，则设置value |  |
| get key | 获取key对应的string值,如果key不存在返回nil |  |
| getset key value | 设置key的值，并返回key的旧值。如果key不存在返回nil |  |
| mget key1 key2 ... keyN | 一次获取多个key的值，如果对应key不存在，则对应返回nil。 |  |
| mset key1 value1 ... keyN valueN | 一次设置多个key的值，成功返回1表示所有的值都设置了，失败返回0表示没有任何值被设置 |  |
| msetnx key1 value1 ... keyN valueN | 如果key不存在，则设置。可以支持多个key-value对 |  |
| incr key | 对key的值做加法操作,并返回新的值。注意incr一个不是int的value会返回错误，incr一个不存在的key，则设置key为1 |  |
| decr key | 对key的值做减法操作，并返回新的值。 |  |
| incrby key integer | 功能与incr类似，只是每次相加指定数值。key不存在时候会设置key，并认为原来的value是 0 |  |
| decrby key integer | 功能与decr类似，只是每次相减指定数值。 |  |
| append key value | 给指定key的字符串值追加value,返回新字符串值的长度。 |  |
| substr key start end | 返回截取过的key的字符串值,注意并不修改key的值。下标是从0开始的。 |  |

#### List 相关命令
List 类型其实就是一个每个子元素都是string类型的双向链表。我们可以通过push，pop操作从链表的头部或者尾部添加删除元素。这使得list既可以用作栈，也可以用作队列。

| 命令 | 说明 | 备注 |
| --- | --- | --- |
| lpush key value | 从左边插入数据 |  |
| rpush key value | 从右边插入数据 |  |
| llen key | 获取列表的长度 |  |
| lpop key | 弹出最左边的数据 |  |
| rpop key | 弹出最右边的数据 |  |

#### Sets 相关命令
Sets 是 string 类型的无序集合，包含的数据不能重复。Set元素最大可以包含(2的32次方 - 1)个元素。

| 命令 | 说明 | 备注 |
| --- | --- | --- |
| sadd key member | 添加一个string元素到，key对应的set集合中，成功返回1。如果元素以及在集合中返回0，key对应的set不存在返回错误 |  |
| srem key member | 从key对应set中移除给定元素，成功返回1，如果member在集合中不存在或者key不存在返回0，如果key对应的不是set类型的值返回错误 |  |
| spop key | 删除并返回key对应set中随机的一个元素,如果set是空或者key不存在返回nil |  |
| srandmember key | 随机取set中的一个元素，但是不删除元素 |  |
| smove srckey dstkey member | 从srckey对应set中移除member并添加到dstkey对应set中，整个操作是原子的。成功返回1,如果member在srckey中不存在返回0，如果key不是set类型返回错误 |  |
| scard key | 返回set的元素个数，如果set是空或者key不存在返回0 |  |
| sismember key member | 判断member是否在set中，存在返回1，0表示不存在或者key不存在 |  |
| sinter | key1 key2...keyN 返回所有给定key的交集 |  |
| sinterstore dstkey key1...keyN | 类似sinter，但是会同时将交集存到dstkey下 |  |


## 管理命令

### 查看版本信息
```sh
$ telnet 127.0.0.1 6379
info
$3286
# Server
redis_version:5.0.4
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:c6d7e1572d62bb79
redis_mode:standalone
os:Linux 4.9.93-linuxkit-aufs x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:6.3.0
process_id:1
run_id:96ed68c2f2b5bb22d872e747b4af6ed2aefb0a55
tcp_port:6379
uptime_in_seconds:67
uptime_in_days:0
hz:10
configured_hz:10
lru_clock:16908
executable:/data/redis-server
config_file:
```

### 选择数据库

### 实时监控
```sh
$ telnet 127.0.0.1 6379
monitor
```

### Key检索
查看所有的key
```sh
> keys *
> keys mo* //查看mo开头的keys
```

## Redis 配置

### 过期清理策略
redis的`maxmemory`参数可以控制最大可用内存大小。当使用的内存超过最大可用内存后，会触发`maxmemory-policy`，其默认值是`noeviction`。


| 规则名称 | 规则说明 |
| --- | --- |
| volatile-lru | 从设置了生存时间的数据集中，使用LRU（Least Recently Used）算法挑选最近最少使用的数据删除 |
| volatile-lfu | 从设置了生存时间的数据集中，使用LFU算法挑选最近使用次数最少的数据删除 |
| volatile-random |  |
| volatile-ttl | 从设置了生存时间的数据集中，挑选离过期时间最近的数据删除 |
| allkeys-lru | 从所有数据集中，使用LRU（Least Recently Used）算法挑选最近最少使用的数据删除 |
| allkeys-lfu |  |
| allkeys-random |  |
| noeviction |  |

> redis中并不会准确的删除所有键中最近最少使用的键，而是随机抽取3个键，删除这三个键中最近最少使用的键。


## 相关资源
1. [https://redis.io](https://redis.io)
2. [关于redis，学会这8点就够了](https://blog.csdn.net/middleware2018/article/details/80355418)
3. [5.Redis内存满了的几种解决方法（内存淘汰策略与Redis集群）](https://blog.csdn.net/u014590757/article/details/79788076)
4. [Redis内存释放策略和过期键删除的策略](https://blog.csdn.net/libafei/article/details/80311372)
5. [Docker安装运行Redis](https://www.cnblogs.com/zhzhlong/p/9465670.html)
6. [Redis Keys 命令 - 查找所有符合给定模式( pattern)的 key](https://www.redis.net.cn/order/3535.html)
7. [转：Redis使用认证密码登录](https://www.cnblogs.com/machanghai/p/5497043.html)
8. [redis最基础的入门教程](https://www.cnblogs.com/xiaohuiduan/p/11394505.html)