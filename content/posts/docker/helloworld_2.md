---
title: Dockerfile入门：用Dockerfile创建CentOS+Nginx - Docker实践教程(2)
author: Kenny
description: Docker极简入门(2)
date: 2016-05-26
categories:
  - docker
tags:
  - docker
  - dockerfile
  - helloworld
slug: helloworld_2
---

[上一篇文章](/posts/docker/helloworld_1) 讲了docker的hello world以及常用的几个docker命令，这篇中我们来创建image。
## Dockerfile
首先创建一个 **Dockerfile** 并进入编辑模式：
``` bash
$ cd /path/to/some/folder
$ touch Dockerfile
$ vi Dockerfile
```
编辑文件内容如下：
``` bash
FROM centos:latest
MAINTAINER Kenny Yao <k@m.fish>

ADD nginx.repo /etc/yum.repos.d/nginx.repo
RUN yum install -y nginx
RUN yum clean all
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx"]
```
**FROM**：Dockerfile文件都是以 **FROM** 开头的，指定此镜像以哪个基础镜像创建，我们以 **centos** 的最新版本 **latest** 建立此镜像。**MAINTAINER** 为该镜像的维护者的信息。
**RUN**：在创建镜像时运行命令；
**ADD**：向镜像中添加本地文件；
**EXPOSE**：指定容器在运行时监听的端口。
**daemon off;**：该指令告诉nginx在前台运行，更多说明参照：[nginx.org](http://nginx.org/en/docs/ngx_core_module.html#daemon)，以及这条Stackoverflow问题：[What is the difference between nginx daemon on/off option?](http://stackoverflow.com/questions/25970711/what-is-the-difference-between-nginx-daemon-on-off-option)，如果不在nginx.conf中加这行，也可以在CMD命令中这样写：
``` bash
CMD ["nginx","-g","daemon off;"]
```
**CMD**：提供了容器默认的执行命令。

所以我们这个项目的文件夹里有两个文件：
![文件夹内容](/img/docker/docker2-1.png)

下面我们用我们的Docker建立一个镜像，使用 **docker build** 命令：
``` bash
$ docker build -t thatk/centos-nginx:v1 .
```
**-t**：表示我们的新镜像属于 **thatk** 用户，镜像名字是 **centos-nginx**，标签是 **v1**。
**.**：我们也需要指定Dockerfile的位置，**.** 表示在当前目录。
接下来你将看到Dockerfile中的每条指令一步一步的执行。
等待命令全部执行完成，我们的镜像也就创建成功了，我们运行 **docker images** 来查看所有镜像：
``` bash
$ docker images
```
![查看新镜像](/img/docker/docker2-2.png)
我们可以利用新建立的镜像来创建一个容器：
``` bash
$ docker run -d -p 8888:80 --name mynginx thatk/centos-nginx:v1
```
**-p**：告诉Docker来进行端口映射，将容器的80端口映射到本机的8888端口。对 **docker run -p** 的详细说明参考官方解释：[Running a web application in Docker](https://docs.docker.com/engine/userguide/containers/usingdocker/#running-a-web-application-in-docker)。
我们可以访问本地docker主机的8888端口，来查看刚才创建的nginx服务是否启动成功：
![nginx running](/img/docker/docker2-3.png)
ok，第一个docker image创建成功。
可以把你本地的镜像推送到 [Docker Hub](https://hub.docker.com) 上，首先需要有一个docker hub帐号（用户名跟镜像用户名相同），并在本地登录，使用命令：
``` bash
$ docker login --username=thatk --password=MyPassword
```
然后使用 **push** 命令来推送：
``` bash
$ docker push thatk/centos-nginx
```

最后上一个拓展阅读：[Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)。官方版本的《Dockerfile最佳实践》，建议写过一个比较复杂的dockerfile之后再阅读，可以指导你写出更规范的dockerfile。
