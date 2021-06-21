---
title: "RBAC权限管理"
tags: ["Python","Django"]
categories: ["总结 · 笔记"]
date: "2020-05-18"
cover : /img/notepic.jpg
---

### 一、RBAC模型是什么？

RBAC是一套成熟的权限模型。在传统权限模型中，我们直接把权限赋予用户。而在RBAC中，增加了“角色”的概念，我们首先把权限赋予角色，再把角色赋予用户。这样，由于增加了角色，授权会更加灵活方便。在RBAC中，根据权限的复杂程度，又可分为RBAC0、RBAC1、RBAC2、RBAC3。其中，RBAC0是基础，RBAC1、RBAC2、RBAC3都是以RBAC0为基础的升级。我们可以根据自家产品权限的复杂程度，选取适合的权限模型。

![RBAC](https://wangxs020202.gitee.io/images/me/rbca1.png)

### 二、基本模型RBAC0

##### 解析：

RBAC0是基础，很多产品只需基于RBAC0就可以搭建权限模型了。在这个模型中，我们把权限赋予角色，再把角色赋予用户。用户和角色，角色和权限都是多对多的关系。用户拥有的权限等于他所有的角色持有权限之和。

![RBAC](https://wangxs020202.gitee.io/images/me/rbca2.png)

##### 举例：

譬如我们做一款企业管理产品，如果按传统权限模型，给每一个用户赋予权限则会非常麻烦，并且做不到批量修改用户权限。这时候，可以抽象出几个角色，譬如销售经理、财务经理、市场经理等，然后把权限分配给这些角色，再把角色赋予用户。这样无论是分配权限还是以后的修改权限，只需要修改用户和角色的关系，或角色和权限的关系即可，更加灵活方便。此外，如果一个用户有多个角色，譬如王先生既负责销售部也负责市场部，那么可以给王先生赋予两个角色，即销售经理+市场经理，这样他就拥有这两个角色的所有权限。

### 三、角色分层模型RBAC1

##### 解析：

RBAC1建立在RBAC0基础之上，在角色中引入了继承的概念。简单理解就是，给角色可以分成几个等级，每个等级权限不同，从而实现更细粒度的权限管理

![RBAC](https://wangxs020202.gitee.io/images/me/rbca3.png)

##### 举例：

基于之前RBAC0的例子，我们又发现一个公司的销售经理可能是分几个等级的，譬如除了销售经理，还有销售副经理，而销售副经理只有销售经理的部分权限。这时候，我们就可以采用RBAC1的分级模型，把销售经理这个角色分成多个等级，给销售副经理赋予较低的等级即可。

### 四、角色限制模型RBAC2

##### 解析：

RBAC2同样建立在RBAC0基础之上，仅是对用户、角色和权限三者之间增加了一些限制。这些限制可以分成两类，即静态职责分离SSD(Static Separation of Duty)和动态职责分离DSD(Dynamic Separation of Duty)。具体限制如下图：

![RBAC](https://wangxs020202.gitee.io/images/me/rbca4.png)

##### 举例：

还是基于之前RBAC0的例子，我们又发现有些角色之间是需要互斥的，譬如给一个用户分配了销售经理的角色，就不能给他再赋予财务经理的角色了，否则他即可以录入合同又能自己审核合同；再譬如，有些公司对角色的升级十分看重，一个销售员要想升级到销售经理，必须先升级到销售主管，这时候就要采用先决条件限制了。

### 五、统一模型RBAC3

##### 解析：

RBAC3是RBAC1和RBAC2的合集，所以RBAC3既有角色分层，也包括可以增加各种限制。

![RBAC](https://wangxs020202.gitee.io/images/me/rbca5.png)

##### 举例：

这个就不举例了，统一模型RBAC3可以解决上面三个例子的所有问题。当然，只有在系统对权限要求非常复杂时，才考虑使用此权限模型。


### 六、基于RBAC的延展--用户组

##### 解析：

基于RBAC模型，还可以适当延展，使其更适合我们的产品。譬如增加用户组概念，直接给用户组分配角色，再把用户加入用户组。这样用户除了拥有自身的权限外，还拥有了所属用户组的所有权限。

![RBAC](https://wangxs020202.gitee.io/images/me/rbca6.png)

##### 举例：

譬如，我们可以把一个部门看成一个用户组，如销售部，财务部，再给这个部门直接赋予角色，使部门拥有部门权限，这样这个部门的所有用户都有了部门权限。用户组概念可以更方便的给群体用户授权，且不影响用户本来就拥有的角色权限。


# 设计表结构

models中创建类：五个类，七张表

　角色表：
　用户表：
　权限表：
　组表：
　菜单表：

角色表和权限表是多对多的关系（一个角色可以有多个权限，一个权限可以对应多个角色）

用户表和角色表是多对多的关系（一个用户可以有多个角色，一个角色有多个用户）

```
from django.db import models

# Create your models here.
class Role(models.Model):
    '''
    角色表
    '''
    title = models.CharField(max_length=32,verbose_name="角色名")
    permissions = models.ManyToManyField(to="Permission",verbose_name="具有的所有权限", blank=True)  # 建立用户表和角色表的多对多关系

    def __str__(self):
        return self.title

    class Meta:
        verbose_name_plural = "角色表"


class Group(models.Model):
    caption = models.CharField(max_length=32,verbose_name="组名称")
    menu = models.ForeignKey(to="Menu",verbose_name="所属菜单",default=1,related_name="menu")
class Menu(models.Model):
    title = models.CharField(max_length=32)

class Permission(models.Model):
    '''
    权限表
    '''
    title = models.CharField(max_length=32,verbose_name="标题")
    url = models.CharField(max_length=64,verbose_name="带正则的URL")
    # is_mune = models.BooleanField(verbose_name="是否是菜单",default=0)
    menu_gp = models.ForeignKey(verbose_name="组内菜单",to="Permission",blank=True,null=True)  #自关联
    #主页就可以设置为菜单，当点击菜单的时候才可以做具体的操作
    codes = models.CharField(max_length=32,verbose_name="代码",default=1)
    group = models.ForeignKey(to="Group",verbose_name="所属组",null=True)  #新添加的字段记得设置默认值

    def __str__(self):
        return self.title

    class Meta:
        '''中文显示'''
        verbose_name_plural = "权限表"
class UserInfo(models.Model):
    '''
    用户表
    '''
    username = models.CharField(max_length=32,verbose_name="用户名")
    password = models.CharField(max_length=64,verbose_name="密码")
    email = models.CharField(max_length=32,verbose_name="邮箱")
    roles = models.ManyToManyField(to="Role",verbose_name="具有的所有角色",blank=True)  #建立用户和角色的多对多关系

    def __str__(self):
        return self.username

    class Meta:
        verbose_name_plural = "用户表"
```