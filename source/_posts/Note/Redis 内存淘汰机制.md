---
title: "redis数据淘汰机制"
tags: ["Redis"]
categories: ["总结 · 笔记"]
date: "2020-05-22"
cover : /img/notepic.jpg
---


书痴者文必工，艺痴者技必良。——蒲松龄 

----


# Redis 内存淘汰机制

### **一、五种数据淘汰机制的具体实现**

- volatile-lru：使用LRU算法进行数据淘汰（淘汰上次使用时间最早的，且使用次数最少的key），只淘汰设定了有效期的key，从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
- allkeys-lru：使用LRU算法进行数据淘汰，所有的key都可以被淘汰，从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
- volatile-random：随机淘汰数据，只淘汰设定了有效期的key，从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
- allkeys-random：随机淘汰数据，所有的key都可以被淘汰，从数据集（server.db[i].dict）中任意选择数据淘汰
- volatile-ttl：淘汰剩余有效期最短的key，从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
- 

### **二、设置redis的最大存储空间**

内存的淘汰机制的初衷是为了更好地使用内存，用一定的缓存miss来换取内存的使用效率。

**首先要配置maxmemory值**

1.客户端发起了需要申请更多内存的命令（如set）。

2.Redis检查内存使用情况，如果已使用的内存大于maxmemory则开始根据用户配置的不同淘汰策略来淘汰内存（key），从而换取一定的内存。

3.如果上面都没问题，则这个命令执行成功。

maxmemory为0的时候表示我们对Redis的内存使用没有限制。



配置方法：

```python
maxmemory-policy volatile-lru   #默认是noeviction，即不进行数据淘汰
```

