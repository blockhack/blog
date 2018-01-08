---
title: Go语言构建Socket应用
author: Kenny
description: 如何用Go语言来构建简单的Socket应用，实现一个简单的聊天程序。
date: 2018-01-08T11:48:07+08:00
tags: [Socket]
categories:
  - Development
  - Golang
slug: socket
weight: 10
draft: true
---

本文主要介绍如何用Go语言来构建简单的Socket应用。

Go语言实现Socket其实很容易，```net```包中对网络协议的封装已经非常完善。当我们谈到Socket的时候，一般场景指的是服务端和客户端的"双向通信"，而通信就需要建立连接通道。Go语言中这个“通道”可以用```net```包中的类型```Conn```来表示，而```TCPConn```和```UDPConn```为```Conn```对应于TCP和UDP协议的实现。本文仅以TCP为例来介绍Go语言的Socket实现（UDP实现差异不大）。

我们使用 [TCPConn](https://golang.org/pkg/net/#TCPConn) 在客户端和服务器端来读写数据以及管理连接，主要用到```Read```、```Write```、```Close```这三个方法：
```golang
func (c *TCPConn) Read(b []byte) (int, error)
func (c *TCPConn) Write(b []byte) (int, error)
func (c *TCPConn) Close() error
```


### 参考文章
- https://astaxie.gitbooks.io/build-web-application-with-golang/content/zh/08.1.html
- https://www.thepolyglotdeveloper.com/2017/05/network-sockets-with-the-go-programming-language/
