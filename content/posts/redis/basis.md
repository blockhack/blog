---
title: "Redis基础"
author: Kenny
description: 本文主要目的是记录日常使用Redis的过程中不断学到的东西，持续更新中。
date: 2018-02-01T10:47:19+08:00
categories:
  - redis
tags:
  - redis
  - basis
slug: basis
---
(未完)
本文持续更新Redis基础知识。

## 基本概念
  - 线程安全：Redis的主要功能都基于单线程模型实现，其所有操作都是原子的，不会因并发产生数据异常；
  - 非阻塞式IO，且大部分命令的算法时间复杂度都是O(1)，因此Redis的速度非常快；

## 数据结构
Redis支持的数据结构：字符串、哈希表、链表、集合、有序集合、位图、Hyperloglogs。
#### String
String是最基础的数据结构，只应用String数据结构来存储的Redis类似Memcached。

String是Redis的基础数据类型，其他所有数据类型在Redis中都体现为String。比如操作整形的方法：INCR/INCRBY/DECR/DECRBY，可以操作保存为String的int64，而且由于Redis线程安全的特性，以上操作可以非常便利的实现高并发场景下的精确控制。应用场景如：秒杀、计数器、发号器等。

#### Hash

#### List
List是一个双向链表，典型的应用场景如消息队列。如果单纯想用作数组，则不建议使用List结构，虽然Redis提供的方法可以实现基本的数组操作，但如LINDEX/LSET/LINSERT等方法时间复杂度为O(N)，谨慎使用。

List还提供了一系列阻塞方法来更方便的实现消息队列，如BLPOP/BRPOP等，即在List为空时阻塞该连接，直到有对象可以POP时再返回。具体说明请参考[官方文档](https://redis.io/topics/data-types-intro)。

## Key
Redis的key是二进制安全的，这意味着可以使用任何二进制序列，从字符串到二进制图片都可以当做key来使用。使用key的一些建议：

  - key应在充分表达含义的基础上控制长度：虽然redis允许key最长512MB，但长度过长的key不仅占用内存，而且在检索的时候更加耗费资源。如果必须要使用一个很长的key值，建议对其进行hash（sha1/md5）处理，这样更节省内容和网络开销。长度过短的key虽然更节省内存，但牺牲了可读性；
  - key命名按照统一的格式规范：例如，object:id:action；

## Value
Value要控制长度，精简数据结构，不存储无用的数据。

  - 存储二进制数据：把数据序列化成二进制数据存储，读取的时候再进行反序列化处理，要注意的是需要选择一种高效的序列化/反序列化方法，否则也会造成不必要的系统开销；
  - 存储JSON：除了二进制数据，通常还会使用JSON字符串存储，为了节省内存，可以选择将其压缩后再存储。

## 性能优化
  - 避免使用计算复杂度高的命令：使用高耗时的Redis命令是很危险的，会占用唯一的一个线程的大量处理时间，导致所有的请求都被拖慢（例如时间复杂度为O(N)的KEYS命令，应避免在生产环境中使用）；

### 参考文章
  - [An introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro)
  - [5 Key Takeaways for Developing with Redis](https://redislabs.com/blog/5-key-takeaways-for-developing-with-redis/)
  - [缓存那些事](https://tech.meituan.com/cache_about.html)
  - [Redis基础、高级特性与性能调优](https://www.jianshu.com/p/2f14bc570563)
