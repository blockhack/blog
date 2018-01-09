---
title: Go语言构建Socket应用
author: Kenny
description: 如何用Go语言来构建简单的Socket应用，实现一个简单的聊天程序。
date: 2018-01-08
categories:
  - golang
tags:
  - golang
  - socket
slug: socket
draft: true
---

本文主要介绍如何用Go语言来构建Socket应用。

Go语言实现Socket其实很容易，```net```包中对网络协议的封装已经非常完善，直接利用即可。当我们谈到Socket的时候，一般场景指的是服务端和客户端的"双向通信"，而通信就需要建立连接通道。Go语言中这个“通道”可以用```net```包中的类型```Conn```来表示，而```TCPConn```和```UDPConn```为```Conn```对应于TCP和UDP协议的实现。本文仅以TCP为例来介绍Go语言的Socket实现（UDP实现差异不大）。
## Simple Server
我们使用 [TCPConn](https://golang.org/pkg/net/#TCPConn) 在客户端和服务器端来读写数据以及管理连接，主要用到```Read```、```Write```、```Close```这三个方法：
```golang
func (c *TCPConn) Read(b []byte) (int, error)
func (c *TCPConn) Write(b []byte) (int, error)
func (c *TCPConn) Close() error
###
```
下面我们先来创建一个服务端，使用```net```包中的```ListenTCP```方法来建立一个TCP网络监听：
```golang
func ListenTCP(network string, laddr *TCPAddr) (*TCPListener, error)
```
此方法接收两个参数，```network```为TCP网络名字符串，```TCPAddr```为TCP的地址信息，返回一个```TCPListener```即TCP网络监听。```TCPAddr```可以通过```ResolveTCPAddr```方法来构造：
```golang
func ResolveTCPAddr(network, address string) (*TCPAddr, error)
```
服务端代码```server/main.go```如下：
```golang
package main

import (
	"fmt"
	"log"
	"net"
	"os"
)

func main() {
	tcpAddr, _ := net.ResolveTCPAddr("tcp4", ":9000")
	listener, err := net.ListenTCP("tcp", tcpAddr)
	checkError(err)
	for {
		conn, err := listener.Accept() // new client connected
		if err != nil {
			log.Println(err)
			continue // keep on listening
		}
		go handler(conn) // goroutine handle the connection
	}
}

func handler(conn net.Conn) {
	defer conn.Close()
	conn.Write([]byte("Success!"))
}

func checkError(err error) {
	if err != nil {
		fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error())
		os.Exit(1)
	}
}

```
这里我们建立了一个位于本地```9000```端口的TCP网络监听，持续接收新客户端连接，并在客户端连接成功之后返回成功信息并断开本次连接。每当有新的客户端接入的时候，TCPListener的```Accept```（或者```AcceptTCP```）方法会返回当前连接：
```golang
func (l *TCPListener) Accept() (Conn, error)
func (l *TCPListener) AcceptTCP() (*TCPConn, error)
```
## Simple Client
方便测试我们的服务，下面来创建一个客户端程序，Go语言中通过net包中的```DialTCP```函数来建立一个TCP连接，并返回一个TCPConn类型的对象：
```golang
func DialTCP(network string, laddr, raddr *TCPAddr) (*TCPConn, error)
```
客户端代码```client/main.go```如下：
```golang
package main

import (
	"fmt"
	"io/ioutil"
	"net"
	"os"
)

func main() {
	tcpAddr, _ := net.ResolveTCPAddr("tcp4", ":9000")
	conn, err := net.DialTCP("tcp", nil, tcpAddr)
	checkError(err)
	_, err = conn.Write([]byte("Hi there!"))
	checkError(err)
	result, err := ioutil.ReadAll(conn)
	checkError(err)
	fmt.Println(string(result))
	os.Exit(0)
}

func checkError(err error) {
	if err != nil {
		fmt.Fprintf(os.Stderr, "Fatal error: %s", err.Error())
		os.Exit(1)
	}
}

```
### 参考文章
- [Build web application with Golang - Astaxie](https://astaxie.gitbooks.io/build-web-application-with-golang/content/zh/08.1.html)
- [Using Network Sockets With The Go Programming Language - Nic Raboy](https://www.thepolyglotdeveloper.com/2017/05/network-sockets-with-the-go-programming-language/)
