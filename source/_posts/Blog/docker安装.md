---
title: "Windows下安装docker"
tags: ["Docker"]
categories: ["编程 · 技术"]
date: "2020-07-16"
cover : /img/timg.jpg
---

### 序幕

[Docker](https://baike.so.com/doc/8850626-9175652.html) 是一个[开源](https://baike.so.com/doc/4975645-27166090.html)的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html)   并遵从 Apache2.0 协议开源，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 [Linux](https://baike.so.com/doc/5349227-5584683.html)或Windows 机器上，也可以实现[虚拟化](https://baike.so.com/doc/2617474-2763805.html)。容器是完全使用[沙箱](https://baike.so.com/doc/5888674-6101559.html)机制，相互之间不会有任何接口。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

#### 应用场景

- Web 应用的自动化打包和发布。
- 自动化测试和持续集成、发布。
- 在服务型环境中部署和调整数据库或其他的后台应用。
- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境

#### 优点

Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

### 1、快速，一致地交付您的应用程序

Docker 允许开发人员使用您提供的应用程序或服务的本地容器在标准化环境中工作，从而简化了开发的生命周期。

容器非常适合持续集成和持续交付（CI / CD）工作流程，请考虑以下示例方案：

- 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
- 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动或手动测试。
- 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中，以进行测试和验证。
- 测试完成后，将修补程序推送给生产环境，就像将更新的镜像推送到生产环境一样简单。

### 2、响应式部署和扩展

Docker 是基于容器的平台，允许高度可移植的工作负载。Docker 容器可以在开发人员的本机上，数据中心的物理或虚拟机上，云服务上或混合环境中运行。

Docker 的可移植性和轻量级的特性，还可以使您轻松地完成动态管理的工作负担，并根据业务需求指示，实时扩展或拆除应用程序和服务。

### 3、在同一硬件上运行更多工作负载

Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行、经济、高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合于高密度环境以及中小型部署，而您可以用更少的资源做更多的事情。

### 安装

1. 首先我们需要下载`Git`，Git大家应该都有，非常好用的一款`开源`的分布式版本控制系统，如果没有我们后期会出安装教程。

2. 紧接着下载`Docker`安装包，这个我已经下载好，并传上了百度网盘。

   ![docker](https://wangxs020202.gitee.io/images/note/docker1.png)

   **大家可以拉下：链接: https://pan.baidu.com/s/1TC8YLrUsS5JhlmOih8Cl5A 提取码: zph1**

3. 接下来打开压缩包，点击安装包

   **切记安装的时候一定要断掉网络**

   ![docker](https://wangxs020202.gitee.io/images/note/docker2.png)

4. 进入安装页面，取消勾选项，点击`Next`

   ![docker](https://wangxs020202.gitee.io/images/note/docker3.png)

5. 选择安装路径，点击`Next`

   ![docker](https://wangxs020202.gitee.io/images/note/docker4.png)

6. 取消勾选项

   ![docker](https://wangxs020202.gitee.io/images/note/docker5.png)

7. 直接点击`Next`

   ![docker](https://wangxs020202.gitee.io/images/note/docker6.png)

8. 点击`Install`

   ![docker](https://wangxs020202.gitee.io/images/note/docker7.png)

9. 安装成功，会给你安装两个软件

   ![docker](https://wangxs020202.gitee.io/images/note/docker8.png)

   **Oracle VM VirtualBox 是一个虚拟机，不会影响使用，但是必须得**

10. 之后进入你的`Git`bin目录下

    ![docker](https://wangxs020202.gitee.io/images/note/docker9.png)

    **复制路径**

11. 鼠标右键`Docker Quickstart Terminal`，点击`属性`,`图一`,将复制的路径更换到此地方`图二`

    ![docker](https://wangxs020202.gitee.io/images/note/docker10.png)

    ![docker](https://wangxs020202.gitee.io/images/note/docker11.png)

12. 之后解压`DockerToolbox-18.01.0-ce.exe`

    ![docker](https://wangxs020202.gitee.io/images/note/docker12.png)

13. 将解压下来的文件放到下面的路径中

    ![docker](https://wangxs020202.gitee.io/images/note/docker13.png)

14. 之后进入`Docker`

    ![docker](https://wangxs020202.gitee.io/images/note/docker14.png)

    **第一次加载是很慢的**

15. 出现这个小鲸鱼说明启动成功了

    ![docker](https://wangxs020202.gitee.io/images/note/docker15.png)

16. 进入虚拟机，可以看见一个任务正在运行

    ![docker](https://wangxs020202.gitee.io/images/note/docker16.png)

### 结语

docker安装非常简单的，前面断网的原因是他在运行时会自动下载`DockerToolbox-18.01.0-ce.exe`这个文件，这个文件非常大的，所以直接断网。明天发`在Win10系统下利用Docker部署Gunicorn+Flask打造独立镜像`