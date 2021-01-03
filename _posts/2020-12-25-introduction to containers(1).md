---
layout: post
title:  "[学习笔记]容器简介（1）--容器的作用"
date:   2020-12-25 15:00:00 +0800--
categories: [理论研习]
tags:   [学习笔记, cluster&container]
---

### 容器简介

容器（container）是我在2016年接触到的，当时听说是个很厉害的东西，后来在17年的时候我在windows环境下装tensorflow，由于当时tensorflow对windows刚刚开始支持，安装比较麻烦，也用了一个tensorflow的容器。由于目前个人的技术规划，这次我打算系统的学习一下容器，用的是coursera上IBM发布的教程[Introduction to Containers w/ Docker, Kubernetes & OpenShift](https://www.coursera.org/learn/ibm-containers-docker-kubernetes-openshift/home/welcome),这个系列的博客就是我的学习笔记。

首先，容器是一些可执行的软件的集合，是在操作系统之上的虚拟化，其优点是小、快、易移植。

容器引擎和容器就像是邮轮和集装箱：只要能装进集装箱的货物就能装上邮轮，同样，只要能打包进容器的软件，就能在成功安装了容器引擎的设备上运行。

容器的优点在于：

> 轻量级（相对于虚拟机来说）

> 跨平台，易移植

> 支持现代化的开发和构架

> 可复用

容器和虚拟机的对比如下图：

![对比](/images/20201225/dockervsvm.png)

可以看到，虚拟机是直接对接的物理资源，然后再在其上跑操作系统，是对硬件本身的抽象。容器则对接的操作系统，是对操作系统的抽象，故其资源占用要小得多。

容器时候用于微应用、开发环境部署、混合云实现以及应用迁移等。

### Docker简介

Docker是非常有名的创建和运行容器的软件，也就是容器引擎，教程里也将其称为container runtime，姑且翻译为容器生命周期管理软件吧。从图上看，生命周期管理是包含了容器引擎和之上的软件和库更大的一个东西。

![dockerruntime](/images/20201225/dockerruntime.png)

介绍几个docker简单的命令：

```shell
docker build
docker tag
docker run
docker push
docker pull
```

```docker build```可以根据dockerfile的内容创建镜像，思路像makefile。```docker tag```是给镜像命名的，可以用这个命令复制出新的镜像。```docker run```当然是运行镜像的，```docker push/pull```则类似于git的同名命令，是做容器的远程版本管理的。

### 用Docker创建容器

在创建容器的时候，```dockerfile```是容器镜像的蓝本，镜像，严格来说是docker镜像，是包含运行docker容器所需的全部内容的文件。可以说镜像是容器的模板，像程序中的对象，容器是运行的镜像的实例，就像是用类创建的对象实体。

#### Dockerfile指令

镜像是通过dockerfile指令以层为单位创建的，每条指令创建一个镜像层，所有指令执行完后，镜像就创建好了，这些层都是只读文件，是不可修改的。

有了镜像之后，在容器运行时，才会创建可读写执行的容器层。镜像层是可以在不同的容器之间共享的。

#### 容器注册所

这个container registers我也不知道怎么翻译好，就像是github的远程仓库，可以执行push/pull等指令来上传/获取镜像，常用的container registers是dockerhub，IBM也有自己的container registers。

#### 运行容器

创建容器的时候，使用：

```shell
docker build -t my-app:v1
```

这样的命令来创建，其中my-app是你在container registers找到的镜像的名称，-t指定tag名称，就是冒号后面的部分，是自己起的tag名。my-app前面没有加主机名，那么默认就是在dockerhub去获取这个镜像。

build之后，可以使用：

```shell
docker images
```

查看本地已有的镜像。

可以使用：

```shell
docker tag my-app:v1 second-app:v1
```

创建新的镜像。

运行的时候，则是：

```shell
docker run my-app:v1
```

最后，如果想把镜像上传到container registers：

```shell
docker push my-app:v1
```

___




<p align = "right">2020年12月25日</p>
<p align = "right">Hang</p>

