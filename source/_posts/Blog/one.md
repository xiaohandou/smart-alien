---
title: "使用Go Lang+Hugo搭建自己的技术博客与gitee部署"
tags: ["Go","Hugo","Git"]
categories: ["编程 · 技术"]
date: "2020-04-09"
cover : /img/timg.jpg
---

### GoLang

一、Go了解

Go 语言起源 2007 年，并于 2009 年正式对外发布，该项目的三位领导者均是著名的 IT 工程师：Robert Griesemer，参与开发 Java HotSpot 虚拟机；Rob Pike，Go 语言项目总负责人，贝尔实验室 Unix 团队成员，参与的项目包括 Plan 9，Inferno 操作系统和 Limbo 编程语言；Ken Thompson，贝尔实验室 Unix 团队成员，C 语言、Unix 和 Plan 9 的创始人之一，与 Rob Pike 共同开发了 UTF-8 字符集规范 简单说，Golang是Google主导，背后由一群大牛的操刀设计的新时代编程语言，虽然比较年轻，但是前景还是非常明朗的。

二、优点 1、简单，容易上手 2、自带高效的GC机制 3、性能高 4、并发编程 5、资源占用低

------

### Hugo

1、Hugo 是基于 Go 语言的静态网站生成器。，编译后只有一个二进制文件，不用安装任何依赖，这一点让人很舒服。

2、适用于搭建个人blog、公司主页、help等网站，是一种小型的CMS系统。静态站点的好处就是快速、安全、易于部署，方便管理

3、优点： 得益于Go的高性能，性能很快
世界上最快的静态网站生成工具，5秒生成6000个页面 文档为Markdown格式,语法超简单 Hugo 可以做静态文件生成工具，还是高性能web 服务； 丰富的站点迁移工具，可以将wordpress，Ghost，Jekyll，DokuWiki，Blogger轻松迁移至 Hugo 超详细的文档 活跃的社区 更加自由的内容组织方式 丰富的主题模板，可以让你的网站更加炫目多彩 多环境支持：macos ，linux，windows

------

### 安装

至于如何安装Hugo，如何搭建网站，网络上也有不少的教程，这里也不再叙述了，自己写出来的未必有官方文档那样详细，所以建议看官方文档。

[https://gohugo.io/documentation/](https://links.jianshu.com/go?to=https%3A%2F%2Fgohugo.io%2Fdocumentation%2F)

首先，可以去go的官网网站下载安装包 https://golang.org/dl/ 然后直接双击安装即可，不需要配置环境变量，因为安装过程自动配置，安装完毕后，打开命令行，输入

```
go version
go version go1.14.1 windows/amd64
```

显示主版本号即表示安装成功

然后，可以进行hugo的在线源码编译安装，打开命令行，输入下面的命令

```
go get -u -v github.com/spf13/hugogo build -o hugo main.gomv hugo $GOPATH/bin
```

如果你不想在线编译安装，也可以去hugo的官网 https://github.com/gohugoio/hugo/releases 下载稳定版的压缩包，解压之后配置一下环境变量也可以

装完以后，在命令行内输入

```
hugo version
Hugo Static Site Generator v0.68.3-157669A0 windows/amd64 BuildDate: 2020-03-24T12:04:36Z
```

打印出版本号即表示hugo安装成功

在命令行中输入命令

```
hugo new site hugo_blog
```

就生成了一个名字为hugo_blog的新站点，可以感受到速度非常快，和vue.js创建新站点的速度比起来简直天差地别

创建网站后，需要下载主题，[https://www.gohugo.org/theme/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.gohugo.org%2Ftheme%2F)

如果本地网站目录中没有`themes`目录的话，自己创建一个，然后下载主题到该目录内。

我个人使用的主题是https://github.com/liuzc/LeaveIt

```
cd themesgit clone https://github.com/liuzc/LeaveIt
```

一般来说，下载的主题都会自带一个配置样例的，具体的配置参考样例即可。或者可以参考下面这个配置样例： [https://github.com/gohugoio/hugo/blob/master/docs/config.toml](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgohugoio%2Fhugo%2Fblob%2Fmaster%2Fdocs%2Fconfig.toml)

编译： 进入站点根目录，然后执行：

```
hugo
```

没错，只要输入命令回车即可，都不用带什么参数就可以生成网站的静态文件，并存放在`public`文件夹中，非常简单。

之后就非常简单了

------

### 使用Gitee Page发布网站

我使用的是gitee，你们也可以使用Github

进入gitee https://gitee.com/之后创建之间的仓库

之后打开命令行，进入`public`文件中,根据gitee的提示进行操作连接自己的数据库

最后进入自己的仓库找到服务，点击`Gitee Page`进行部署，你就有了属于自己的技术博客