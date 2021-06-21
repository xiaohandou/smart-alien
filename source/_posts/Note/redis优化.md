---
title: "redis优化"
tags: ["Redis"]
categories: ["总结 · 笔记"]
date: "2020-05-23"
cover : /img/notepic.jpg
---


聪明出于勤奋，天才在于积累。——华罗庚 

----

### **数据持久化**

[数据持久化](https://www.sirxs.cn/2020/05/21/Note/reids%E6%8C%81%E4%B9%85%E5%8C%96/)

### **内存优化**

设置*maxmemory*。设置Redis使用的最大物理内存，即Redis在占用*maxmemory*大小的内存之后就开始拒绝后续的写入请求，该参数可以确保Redis因为使用了大量内存严重影响速度或者发生*OOM*。此外，可以使用`info`命令查看Redis占用的内存及其它信息。

让键名保持简短。键的长度越长，`Redis`需要存储的数据也就越多

使用短结构。主要Redis的*list*、*hash*、*set*、*zset*这四种数据结构的存储优化。在*Redis3.2*之前，如果列表、散列或者有序集合的长度或者体积较小，*Redis*会选择一种名为*ziplist*的数据结构来存储它们。该结构是列表、散列和有序集合三种不同类型的对象的一种非结构化表示，与Redis在通常情况下使用双向链表来表示列表、使用散列表示散列、使用散列加跳跃表表示有序集合相比，它更加紧凑，避免了存储额外的指针和元数据（比如字符串值的剩余可用空间和结束符"\0"）。但是压缩列表需要在存储的时候进行序列化，读取的时候进行反序列化。以散列为例，在`redis.conf`中，可以进行如下设置

```python
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
```

### **拓展读写能力**

扩展读性能。在`redis.conf`中添加`slaveof host port`即可将其配置为另一台Redis服务器的从服务器。注意，在从服务器连接主服务器的时候，从服务器之前的数据会被清空。可以用这种方式建立从服务器树，扩展其读能力。但这种方式并未做故障转移，高可用Redis部署方案可以参考[Redis Sentinel](https://www.jianshu.com/p/afb678794a0e),[Redis Cluster](https://link.jianshu.com?t=http%3A%2F%2Fredis.io%2Ftopics%2Fcluster-tutorial)和[Codis](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2FCodisLabs%2Fcodis)。

扩展写性能。(1)使用集群分片技术，比如Redis Cluster;(2)单机上运行多个Redis实例。由于Redis是单线程设计，在涉及到cpu bound的操作的时候，可能速度会大大降低。如果服务器的cpu、io资源充足，可以在同一台机器上运行多个Redis服务器。

### **应用程序优化**

应用程序优化部分主要是客户端和Redis交互的一些建议。主要思想是**尽可能减少操作Redis往返的通信次数**。

Redis提供了*Slow Log*功能，可以自动记录耗时较长的命令，`redis.conf`中的配置如下

```python
#执行时间慢于10000毫秒的命令计入Slow Log
slowlog-log-slower-than 10000  
#最大纪录多少条Slow Log
slowlog-max-len 128
```

使用`SLOWLOG GET n`命令，可以输出最近n条慢查询日志。使用`SLOWLOG RESET`命令，可以重置*Slow Log*。