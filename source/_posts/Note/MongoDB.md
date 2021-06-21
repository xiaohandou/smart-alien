---
title: "MongoDB与相关命令"
tags: ["MongoDB"]
categories: ["总结 · 笔记"]
date: "2020-06-04 16:59:18"
cover : /img/notepic.jpg

---

### 序幕

MongoDB（来自于英文单词“Humongous”，中文含义为“庞大”）是可以应用于各种规模的企业、各个行业以及各类应用程序的开源数据库。作为一个适用于敏捷开发的数据库，MongoDB的数据模式可以随着应用程序的发展而灵活地更新。与此同时，它也为开发人员 提供了传统数据库的功能：二级索引，完整的查询系统以及严格一致性等等。 MongoDB能够使企业更加具有敏捷性和可扩展性，各种规模的企业都可以通过使用MongoDB来创建新的应用，提高与客户之间的工作效率，加快产品上市时间，以及降低企业成本。

![mongo](https://www.runoob.com/wp-content/uploads/2013/10/mongodb-logo.png)

MongoDB是专为可扩展性，高性能和高可用性而设计的数据库。它可以从单服务器部署扩展到大型、复杂的多数据中心架构。利用内存计算的优势，MongoDB能够提供高性能的数据读写操作。 MongoDB的本地复制和自动故障转移功能使您的应用程序具有企业级的可靠性和操作灵活性

### MongoDB

#### 特点

- MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。
- 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
- Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
- MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
- MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
- MongoDB安装简单。

#### 安装

- [Windows](https://www.runoob.com/mongodb/mongodb-window-install.html)
- [Linux](https://www.runoob.com/mongodb/mongodb-linux-install.html)
- [Mac OSX](https://www.runoob.com/mongodb/mongodb-osx-install.html)

#### 相关命令

- 创建数据库

  ```python
  use DATABASE_NAME #如果数据库不存在，则创建数据库，否则切换到指定数据库。
  ```

- 查看所有数据库

  ```python
  show dbs 
  ```

- 删除数据库

  ```python
  db.dropDatabase()  #删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。
  ```

- 在表中插入数据

  ```python
  db.COLLECTION_NAME.insert(document)
  或
  db.COLLECTION_NAME.save(document)
  #save()：如果 _id 主键存在则更新数据，如果不存在就插入数据。该方法新版本中已废弃，可以使用 db.collection.insertOne() 或 db.collection.replaceOne() 来代替。
  #insert(): 若插入的数据主键已经存在，则会抛 org.springframework.dao.DuplicateKeyException 异常，提示主键重复，不保存当前数据。
  ```

- 修改表中数据

  ```python
  db.collection.update(
     <query>,
     <update>,
     {
       upsert: <boolean>,
       multi: <boolean>,
       writeConcern: <document>
     }
  )
  #query : update的查询条件，类似sql update查询内where后面的。
  #update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
  #upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
  #multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
  #writeConcern :可选，抛出异常的级别。
  ```

- 删除表中数据

  ```python
  db.collection.remove(
     <query>,
     {
       justOne: <boolean>,
       writeConcern: <document>
     }
  )
  #query :（可选）删除的文档的条件。
  #justOne : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
  #writeConcern :（可选）抛出异常的级别。
  ```

- 查询表中数据

  ```python
  db.collection.find(query, projection)
  #query ：可选，使用查询操作符指定查询条件
  #projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。
  ```

- 条件操作符

  ```python
  (>) 大于 - $gt
  (<) 小于 - $lt
  (>=) 大于等于 - $gte
  (<= ) 小于等于 - $lte
  ```

- 排序

  ```python
  db.COLLECTION_NAME.find().sort({KEY:1})
  #在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。
  ```

  

### 总结

MongoDB快速开发文档：

1. [菜鸟教程](https://www.runoob.com/mongodb/mongodb-tutorial.html)
2. [官网](https://www.mongodb.com/cn)

