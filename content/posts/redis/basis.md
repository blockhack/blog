---
title: "Redis基础及最佳实践"
author: Kenny
description: 本文主要目的是记录日常使用Redis的过程中不断学到的东西，持续更新中。
date: 2018-02-01T10:47:19+08:00
categories:
  - redis
tags:
  - redis
  - basis
slug: basis
draft: true
---

本文持续更新在使用Redis过程中积累的经验。


## 数据结构
Redis一共有5种常用的数据结构：string、hash、list、set、zset(sorted set)，

## Keys
Redis的key是二进制安全的，这意味着可以使用任何二进制序列，从字符串到二进制图片都可以当做key来使用。使用key的一些建议：

  - key应在充分表达含义的基础上控制长度：虽然redis允许key最长512MB，但长度过长的key不仅占用内存，而且在检索的时候更加耗费资源。如果必须要使用一个很长的key值，建议对其进行hash（sha1/md5）处理，这样更节省内容和网络开销。长度过短的key虽然更节省内存，但牺牲了可读性；
  - key命名按照统一的格式规范：例如，object:id:action；


### 参考文章
  - [An introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro)
  - [5 Key Takeaways for Developing with Redis](https://redislabs.com/blog/5-key-takeaways-for-developing-with-redis/)
  - [缓存那些事](https://tech.meituan.com/cache_about.html)
