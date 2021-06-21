---
title: "RESTful API"
tags: ["Python"]
categories: ["总结 · 笔记"]
date: "2020-05-29"
cover : /img/notepic.jpg
---

### 一、什么是REST

![restful](https://wangxs020202.gitee.io/images/me/restful.gif)



REST全称是Representational State Transfer(表述性状态转移)，是HTTP规范的标准。REST指的是一组架构约束条件和原则。如果一个架构符合REST的约束条件和原则，我们就称它为RESTful架构。 但是理论上REST架构风格并不是绑定在HTTP上，只不过目前HTTP是唯一与REST相关的实例。 所以我们这里描述的REST也是通过HTTP实现的REST。

### 二、为什么要用REST框架

在初学者眼中HTTP只不过是一种通道，他们通常不会遵循REST风格，而将代码乱写一通，想起一个接口写一个。代码整体效果给人看上去是杂烂无章，无从下手。

就像我们去餐厅点餐

```python
顾客A：‘给我来一份海鲜大咖’
传参
{
    {'ordername':'海鲜大咖'}
}

前台接收到信息

给你返回的信息(订单编号)

{
    'orederid':123
}
这样一次点餐流程完成

顾客A：‘再给我来一份烧烤’
传参
{
    {'ordername':'烧烤'}
}

前台接收到信息

再次给你返回的信息(订单编号)

{
    'orderid':456
}
```

这样每个顾客来点餐都会再次传参，再次生产订单。

像这个样子**面向过程**写出的代码不仅量大，复杂，而且给其他人看不一定能看懂，所以初学者开始进阶了

```python
/order  #订单相关
/cards  #会员卡
顾客A：‘给我来一份海鲜大咖，我有会员卡’

传参

/order
{
    'addoreder':{
        'ordername':'海鲜大咖'
    }
}

前台接收到信息

再次给你返回的信息(订单编号)

{
    'orederid':123
}

/cards

{
    'cardis':666
}

```

每次**面向资源**通过参数把订单与会员卡分开，是代码清晰了不少但是代码量还是太多了

当初学者初次理解rest与http协议，开始使用method控制代码

```python
顾客A：‘给我来个海鲜大咖，我有会员卡’

GET /orders
{
	"orderid":123
}

POST /orders 新增

{
	"ordername":"海鲜大咖"
}

顾客A：‘算了，我不想要海鲜大咖了，给我换一份烧烤’

PUT /orders 修改
{
	"orderid":123
	"ordername":"烧烤"
}

过了很长时间，顾客A的烧烤还没好！！！

顾客A：‘怎么还没好，我不要了，走了’

DELETE /orders 删除
{
	"orderid":123
}
```

这样通过method控制代码，代码以及变得很清晰，简单了

但是**大佬**都是这样写代码

```python
DELETE /orders  orderid={orderid}

POST /orders

{
	
	"orderid":123123,
	"delete":{
		"method":"DELETE"
		"url":'/orders'
		"orderid":123123
	},
	"edit":{
		"method":"PUT"
		"url":'/orders'
		"orderid":123123
	}


}
```

写一个接口，就能让别人知道他这个接口还可以做很多操作，真正实api,restful风格的接口 给用户使用者带来很大便利和最高的用户体验



### 三、怎么使用REST风格写接口

1.把**资源**和**URL**结合

要让一个资源可以被识别，需要有个唯一标识，在Web中这个唯一标识就是URIURI既可以看成是资源的地址，也可以看成是资源的名称。把资源和URL结合起来，使资源充分利用。就像GitHub上的URL：

- https://github.com/git
- https://github.com/git/git
- https://github.com/git/git/blob/master/block-sha1/sha1.h
- https://github.com/git/git/commit/e3af72cdafab5993d18fae056f87e1d675913d08
- https://github.com/git/git/pulls
- https://github.com/git/git/pulls?state=closed
- https://github.com/git/git/compare/master…next

2.统一资源接口

RESTful架构应该遵循统一接口原则，统一接口包含了一组受限的预定义的操作，不论什么样的资源，都是通过使用相同的接口进行资源的访问。接口应该使用标准的HTTP方法如GET，PUT和POST，并遵循这些方法的语义。

下面列出了GET，DELETE，PUT和POST的典型用法:

## GET

- 安全且幂等
- 获取表示
- 变更时获取表示（缓存）

- 200（OK） - 表示已在响应中发出

- 204（无内容） - 资源有空表示
- 301（Moved Permanently） - 资源的URI已被更新
- 303（See Other） - 其他（如，负载均衡）
- 304（not modified）- 资源未更改（缓存）
- 400 （bad request）- 指代坏请求（如，参数错误）
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务端当前无法处理请求

## POST

- 不安全且不幂等
- 使用服务端管理的（自动产生）的实例号创建资源
- 创建子资源
- 部分更新资源
- 如果没有被修改，则不过更新资源（乐观锁）

- 200（OK）- 如果现有资源已被更改

- 201（created）- 如果新资源被创建
- 202（accepted）- 已接受处理请求但尚未完成（异步处理）
- 301（Moved Permanently）- 资源的URI被更新
- 303（See Other）- 其他（如，负载均衡）
- 400（bad request）- 指代坏请求
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 409 （conflict）- 通用冲突
- 412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type）- 接受到的表示不受支持
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务当前无法处理请求

## PUT

- 不安全但幂等
- 用客户端管理的实例号创建一个资源
- 通过替换的方式更新资源
- 如果未被修改，则更新资源（乐观锁）

- 200 （OK）- 如果已存在资源被更改

- 201 （created）- 如果新资源被创建
- 301（Moved Permanently）- 资源的URI已更改
- 303 （See Other）- 其他（如，负载均衡）
- 400 （bad request）- 指代坏请求
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 409 （conflict）- 通用冲突
- 412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type）- 接受到的表示不受支持
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务当前无法处理请求

## DELETE

- 不安全但幂等

- 删除资源

- 200 （OK）- 资源已被删除

- 301 （Moved Permanently）- 资源的URI已更改

- 303 （See Other）- 其他，如负载均衡

- 400 （bad request）- 指代坏请求

- 404 （not found）- 资源不存在

- 409 （conflict）- 通用冲突

- 500 （internal server error）- 通用错误响应

- 503 （Service Unavailable）- 服务端当前无法处理请求

  

  

  *注：GitHubURL，典型用法来自菜鸟教程

### 四、RESTful 详解

1. [菜鸟教程](https://www.runoob.com/w3cnote/restful-architecture.html)
2. [简书](https://www.jianshu.com/p/84568e364ee8)
3. [掘金](https://juejin.im/entry/58c0fa6b128fe100601f4735)

