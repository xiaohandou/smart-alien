---
title: "Scrapy"
tags: ["Python","Scrapy"]
categories: ["编程 · 技术"]
date: "2020-04-11"
cover : /img/timg.jpg
---

### Scrapy是用python实现的一个为了爬取网站数据，提取结构性数据而编写的应用框架。使用Twisted高效异步网络框架来处理网络通信。

### 接下来我们说说scrapy的安装及其他

1.scrapy安装与环境



1.在安装scrapy前需要安装好相应的依赖库, 再安装scrapy, 具体安装步骤如下:    
    (1).安装lxml库: pip install lxml 

    (2).安装wheel: pip install wheel

    (3).安装twisted: pip install twisted文件路径(twisted需下载后本地安装,下载地址:http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted)(版本选择如下图,版本后面有解释,请根据自己实际选择)

    (4).安装pywin32: pip install pywin32(注意:以上安装步骤一定要确保每一步安装都成功,没有报错信息,如有报错自行百度解决)    

    (5).安装scrapy: pip install scrapy(注意:以上安装步骤一定要确保每一步安装都成功,没有报错信息,如有报错自行百度解决)   

    (6).成功验证:在cmd命令行输入scrapy,显示Scrapy1.6.0-no active project,证明安装成功 



![环境](https://wangxs020202.gitee.io/images/me/123.png)

2.scrapy了解

一、五大核心组件

```
	1.引擎: 负责数据流传递, 各个组件之间的通信	
    2.spider: 定义了爬取的行为和数据解析	
    3.调度器: scheduler, 负责调度所有的请求	
    4.下载器: 负责发送网络请求, 返回响应数据	
    5.管道: item Pipeline ---> item: 定义了要进行数据持久化的字段 ---> pipeline: 与数据库进行交互, 将数据存储在数据库中
```

二、scrapy框架的数据流:

```
    spider -->  引擎  -->  调度器  -->  引擎  -->  下载器  -->  互联网 -->  下载器 -->  引擎  -->  spider  -->  引擎  -->  管道  -->  入库
```

![流程](https://wangxs020202.gitee.io/images/me/scrapy.png)

三、scrapy中间件

```
            # 中间件分类:  
                      
            - 下载中间件: DownloadMiddleware            
            - 爬虫中间件: SpiderMiddleware 

            # 中间件的作用:                
            - 下载中间件: 拦截请求与响应, 篡改请求与响应                
            - 爬虫中间件: 拦截请求与响应, 拦截管道item, 篡改请求与响应, 处理item   

            # 下载中间件的主要方法:  

            process_request            
            process_response            
            process_exception
```