---
title: Docker入门 - Docker实践教程(1)
author: Kenny
description: Docker极简入门(1)
date: 2016-05-25
categories:
  - docker
tags:
  - docker
  - helloworld
slug: helloworld_1
---

接触docker有一段时间了，但之前的使用仅限于使用别人的镜像搭建shadowsocks和pptp，并没有深入研究。最近开始研究docker，先从搭建公司产品的开发环境入手，期望能探索一下docker在生产/测试环境中的最佳实践，提升公司研发团队的研发体验。所以有了这个系列文章。
Docker在各种环境的安装指南：[Docker Installation](https://docs.docker.com/engine/installation)
Mac用户请安装官方提供的：[Docker Toolbox](https://docs.docker.com/engine/installation/mac/)
貌似也可以安装（并没有用过）：[Boot2docker](http://boot2docker.io)
## Hello World
安装完成之后，可以输入下面的命令来查看Docker的信息：
``` bash
$ docker info
```
或仅查看docker的版本信息：
``` bash
$ docker version
```
获得docker命令帮助：
``` bash
$ docker --help
```
获得特定命令帮助（以run命令为例）：
``` bash
$ docker run --help
```
下面我们启动一个centos来输出我们的docker hello world：
``` bash
$ docker run centos /bin/echo 'Hello world'
```
**docker run** 命令来运行一个容器（container），**centos** 是我们这次容器里面运行的镜像（image）。当你指定运行一个镜像的时候，docker首先会查找你本地的docker环境是否已存在该镜像，如果不存在，则会去 [Docker Hub](https://hub.docker.com) 拉取（pull）这个镜像的最新版本。或者你可以选择在run某个镜像之前，手动运行 **docker pull some/image** 来下载某个镜像到本地docker环境。已经pull到本地的镜像可以通过 **docker images** 命令来查看。
**/bin/echo 'Hello world'** 是我们在容器中运行的命令。如果想在运行centos之后进入命令行，可以使用以下命令：
``` bash
$ docker run -t -i centos /bin/bash
```
**-t** 命令让docker分配一个伪终端（pseudo-TTY）给当前容器
**-i** 命令让容器持续接收用户输入
**/bin/bash** 大家都懂这是干嘛的
更多**docker run**命令的参数请参看：[docker run](https://docs.docker.com/engine/reference/commandline/run/)，后面也会介绍其他几个用到的参数。

以上几步的运行截图：
![docker hello world](/img/docker/docker1-1.png)
## Docker Daemon
下面我们来创建一个守护程序（daemon）：
``` bash
$ docker run -d centos /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
**-d** 命令让容器在后台运行（to daemonize）
运行一个daemon之后会获得容器的长id
如果你启动了一个或多个daemon，可以通过以下命令来查看各个容器的信息：
``` bash
$ docker ps
```
运行 **docker ps** 之后会获得容器的短id、容器name，以及镜像信息、运行状态等其他信息：
![docker daemon](/img/docker/docker1-2.png)

可以看到docker给刚才创建的容器起了一个随机的name，当然我们也可以选择在 **docker run** 的时候使用 **--name** 参数来指定容器名。

长id、短id、name，都可以用来唯一指定一个容器，比如查看我们刚才创建的容器运行的状况，以下三个命令是等价的：
``` bash
$ docker logs 7984414fda90b87703c33348c12996380807eba5bd0b6c53ac5390897027781a
$ docker logs 7984414fda90
$ docker logs gloomy_leavitt
```
以上规则同样适用于停止一个容器的时候，以下三个命令都可以实现stop操作：
``` bash
$ docker stop 7984414fda90b87703c33348c12996380807eba5bd0b6c53ac5390897027781a
$ docker stop 7984414fda90
$ docker stop gloomy_leavitt
```
这时候再次运行 **docker ps** 可以看到刚才的容器已不存在列表中了
运行 **docker ps -l** 可以看到看到上一个启动的容器（不管是否stop）
运行 **docker ps -a** 可以看到看到全部启动的容器
已经stop的容器可以通过 **docker start** 命令再次运行
``` bash
$ docker start gloomy_leavitt
```
已经stop的容器也可以通过 **docker rm** 命令来删除

同样的，image也可以删除，通过 **docker rmi** 命令，前提是没有依赖该image的容器运行，且没有其他image依赖这个image。若因以上原因无法删除，可以增加 **-f** 命令来强制删除， **-f** 命令同样适用于 **docker rm** 命令。使用 **-f** 删除会导致已启动的相关容器停止运行。

[下一篇文章](/posts/docker/helloworld_2) 会介绍Dockerfile。
