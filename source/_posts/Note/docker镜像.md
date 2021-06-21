---
title: "Docker镜像安装与启动"
tags: ["Python","Vue"]
categories: ["总结 · 笔记"]
date: "2020-04-08"
cover : /img/notepic.jpg
---

取得成就时**坚持不懈**，要比遭到失败时顽强不屈更重要。——拉罗科

------

### Docker介绍

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

### Docker的应用场景

1、Web 应用的自动化打包和发布。

2、自动化测试和持续集成、发布。

3、在服务型环境中部署和调整数据库或其他的后台应用。

4、从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

### Docker 的优点

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

#### 1、快速，一致地交付您的应用程序

#### 2、响应式部署和扩展

#### 3、在同一硬件上运行更多工作负载

### 相关链接

Docker 官网: [https://www.docker.com](https://www.docker.com/)

Github Docker 源码：https://github.com/docker/docker-ce

------

### Docker 安装（win10）

Win10 系统

现在 Docker 有专门的 Win10 专业版系统的安装包，需要开启Hyper-V。

开启 Hyper-V

![hyper1](https://wangxs020202.gitee.io/images/me/hyper1.png)![hyper2](https://wangxs020202.gitee.io/images/me/hyper2.png)![hyper3](https://wangxs020202.gitee.io/images/me/hyper3.png)![hyper4](https://wangxs020202.gitee.io/images/me/hyper4.png)

### 下载Docker

一般情况下，我们可以从Docker官网下载docker安装文件，但是官方网站由于众所周知的原因，不是访问慢，就是下载慢。下载docker安装包动不动就要个把小时，真是极大的影响工作效率。

在这里推荐一个docker的下载（主要是Windows版）地址：

https://oomake.com/download/docker-windows

[http://get.daocloud.io](http://get.daocloud.io/)

### 安装Docker Toolbox

在这一步，你将安装Docker Toolbox。安装后你的系统将会安装以下几个软件：

Windows版的Docker客户端

Docker Toolbox管理工具和ISO镜像

Oracle VM Virtualbox

Git MSYS-git Unix 工具

如果你的电脑已经安装了Virtualbox，那么不需要再重新安装Oracle VM Virtualbox，在安装的时候取消勾选就可以了

Ps:如果你的Virtual box正在运行，那么要先关闭掉它然后再重新运行安装

1. 打开Docker Toolbox页面
2. 点击installer链接进行下载
3. 双击安装包进行安装Docker Toolbox
4. 点击下一步，进行安装就可以了

安装完成

![docker1](https://wangxs020202.gitee.io/images/me/docker1.png)

### 启动 Docker

双击Docker Quickstart Terminal图标，启动一个终端

第一次启动的话你会看到命令行会输出一些东西，等待一下，它会配置Docker Toolbox，之后，当它完成后，你会看到启动成功的画面

![docker2](https://wangxs020202.gitee.io/images/me/docker2.png)

**启动成功**