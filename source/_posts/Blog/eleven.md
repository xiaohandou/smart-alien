---
title: "Redis分布式锁"
tags: ["Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-05-08"
cover : /img/timg.jpg
---


### 超卖解决方案？

1、mysql悲观锁：select_for_updata()

2、mysql乐观锁：

```python
While Ture:
  #查询
  
 。。。

	User.object.filter(原来的条件).updata(现在的条件)


```





#### 分布式锁

**什么是分布式锁？**

​		分布式锁是控制分布式系统之间同步访问共享资源的一种方式。

**什么实用分布式锁？**

​		为了保证共享资源的数据一致性。

**什么场景下使用分布式锁？**

​		数据重要且要保证一致性

### **如何实现分布式锁？**

​	主要介绍使用redis来实现分布式锁

​	

## redis实现分布式锁

# redis事务

### redis事务介绍：

​		1.redis事务可以一次执行多个命令，本质是一组命令的集合。

​		2.一个事务中的所有命令都会序列化，按顺序串行化的执行而不会被其他命令插入

​		**作用：**一个队列中，一次性、顺序性、排他性的执行一系列命令 

### multi指令的使用

  　　1. 下面指令演示了一个完整的事物过程，所有指令在exec前不执行，而是缓存在服务器的一个事物队列中

  　　2. 服务器一旦收到exec指令才开始执行事物队列，执行完毕后一次性返回所有结果

  　　3. 因为redis是单线程的，所以不必担心自己在执行队列是被打断，可以保证这样的“原子性”

　　注：redis事物在遇到指令失败后，后面的指令会继续执行

```python
# Multi 命令用于标记一个事务块的开始事务块内的多条命令会按照先后顺序被放进一个队列当中，最后由 EXEC 命令原子性( atomic )地执行
> multi（开始一个redis事物）
incr books
incr books
> exec （执行事物）
> discard （丢弃事物）
```

**注：mysql的rollback与redis的discard的区别**

   　　　1. mysql回滚为sql全部成功才执行,一条sql失败则全部失败,执行rollback后所有语句造成的影响消失

   　　　2. redis的discard只是结束本次事务,正确命令造成的影响仍然还在.

　　　　　1）redis如果在一个事务中的**命令出现错误**，那么**所有的命令都不会执行**；
　　　　　2）redis如果在一个事务中出现**运行错误**，那么**正确的命令会被执行**。

### **watch 指令作用**

　　　实质：WATCH 只会在数据被其他客户端抢先修改了的情况下通知执行命令的这个客户端（通过 WatchError 异常）但不会阻止其他客户端对数据的修改

   　　　1. **watch其实就是redis提供的一种乐观锁，可以解决并发修改问题**

   　　　2. watch会在事物开始前盯住一个或多个关键变量，当服务器收到exec指令要顺序执行缓存中的事物队列时，redis会检查关键变量自watch后是否被修改

   　　　3. WATCH 只会在数据被其他客户端抢先修改了的情况下通知执行命令的这个客户端（通过 WatchError 异常）但不会阻止其他客户端对数据的修改



### setnx指令（redis的分布式锁）

　　**1、分布式锁**

   　　　　1. 分布式锁本质是占一个坑，当别的进程也要来占坑时发现已经被占，就会放弃或者稍后重试

          2. 占坑一般使用 setnx(set if not exists)指令，只允许一个客户端占坑

                　　　　3. 先来先占，用完了在调用del指令释放坑

```python
> setnx lock:codehole true
.... do something critical ....
> del lock:codehole
```

​				4. 但是这样有一个问题，如果逻辑执行到中间出现异常，可能导致del指令没有被调用，这样就会陷入死锁，锁永远无法释放

   　　　　5. 为了解决死锁问题，我们拿到锁时可以加上一个expire过期时间，这样即使出现异常，当到达过期时间也会自动释放锁

```python
> setnx lock:codehole true
> expire lock:codehole 5
.... do something critical ....
> del lock:codehole
```

​			   6. 这样又有一个问题，setnx和expire是两条指令而不是原子指令，如果两条指令之间进程挂掉依然会出现死锁

   　　　　7. 为了治理上面乱象，在redis 2.8中加入了set指令的扩展参数，使setnx和expire指令可以一起执行

```python
> set lock:codehole true ex 5 nx
''' do something '''
> del lock:codehole
```

### redis解决超卖问题

**1、使用reids的 watch + multi 指令实现**

```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-
import redis
def sale(rs):
    while True:
        with rs.pipeline() as p:
            try:
                p.watch('apple')                   # 监听key值为apple的数据数量改变
                count = int(rs.get('apple'))
                print('拿取到了苹果的数量: %d' % count)
                p.multi()                          # 事务开始
                if count> 0 :                      # 如果此时还有库存
                    p.set('apple', count - 1)
                    p.execute()                    # 执行事务
                p.unwatch()
                break                              # 当库存成功减一或没有库存时跳出执行循环
            except Exception as e:                 # 当出现watch监听值出现修改时，WatchError异常抛出
                print('[Error]: %s' % e)
                continue                           # 继续尝试执行

rs = redis.Redis(host='127.0.0.1', port=6379)      # 连接redis
rs.set('apple',1000)                               # # 首先在redis中设置某商品apple 对应数量value值为1000
sale(rs)
```

**1）原理**

  　　1. 当用户购买时，通过 WATCH 监听用户库存，如果库存在watch监听后发生改变，就会捕获异常而放弃对库存减一操作

  　　2. 如果库存没有监听到变化并且数量大于1，则库存数量减一，并执行任务

  **2）弊端**

  　　1. Redis 在尝试完成一个事务的时候，可能会因为事务的失败而重复尝试重新执行

  　　2. 保证商品的库存量正确是一件很重要的事情，但是单纯的使用 WATCH 这样的机制对服务器压力过大

**2、使用reids的 watch + multi + setnx 指令实现**

　　**1）为什么要自己构建锁**

   1. 然有类似的 SETNX 命令可以实现 Redis 中的锁的功能，但他锁提供的机制并不完整

      . 并且setnx也不具备分布式锁的一些高级特性，还是得通过我们手动构建

　　**2）创建一个redis锁**

   1. 在 Redis 中，可以通过使用 SETNX 命令来构建锁：**rs.setnx(lock_name, uuid值)**

      . 而锁要做的事情就是将一个随机生成的 128 位 UUID 设置位键的值，防止该锁被其他进程获取

　　**3）释放锁**

   1. 锁的删除操作很简单，只需要将对应锁的 key 值获取到的 uuid 结果进行判断验证

      . 符合条件（*判断uuid值*）通过 delete 在 redis 中删除即可，pipe.delete(lockname)

　　　　　*3. 此外当其他用户持有同名锁时，由于 uuid 的不同，经过验证后不会错误释放掉别人的锁*

 　**4）解决锁无法释放问题**

   　　　　　1. 在之前的锁中，还出现这样的问题，比如某个进程持有锁之后突然程序崩溃，那么会导致锁无法释放

　　　　　*2. 而其他进程无法持有锁继续工作，为了解决这样的问题，可以在获取锁的时候加上锁的超时功能*

```python
import redis
import uuid
import time

# 1.初始化连接函数
def get_conn(host="127.0.0.1",port=6379):
    rs = redis.Redis(host=host, port=port)
    return rs

# 2. 构建redis锁
def acquire_lock(rs, lock_name, expire_time=10):
    '''
    rs: 连接对象
    lock_name: 锁标识
    acquire_time: 过期超时时间
    return -> False 获锁失败 or True 获锁成功
    '''
    identifier = str(uuid.uuid4())
    end = time.time() + expire_time
    while time.time() < end:
        # 当获取锁的行为超过有效时间，则退出循环，本次取锁失败，返回False
        if rs.setnx(lock_name, identifier): # 尝试取得锁
            return identifier
        # time.sleep(.001)
        return False

# 3. 释放锁
def release_lock(rs, lockname, identifier):
    '''
    rs: 连接对象
    lockname: 锁标识
    identifier: 锁的value值，用来校验
    '''


    if rs.get(lockname).decode() == identifier:  # 防止其他进程同名锁被误删
        rs.delete(lockname)
        return True            # 删除锁
    else:
        return False           # 删除失败

#有过期时间的锁
def acquire_expire_lock(rs, lock_name, expire_time=10, locked_time=10):
    '''
    rs: 连接对象
    lock_name: 锁标识
    acquire_time: 过期超时时间
    locked_time: 锁的有效时间
    return -> False 获锁失败 or True 获锁成功
    '''
    identifier = str(uuid.uuid4())
    end = time.time() + expire_time
    while time.time() < end:
        # 当获取锁的行为超过有效时间，则退出循环，本次取锁失败，返回False
        if rs.setnx(lock_name, identifier): # 尝试取得锁
            # print('锁已设置: %s' % identifier)
            rs.expire(lock_name, locked_time)
            return identifier
        time.sleep(.001)
    return False


'''在业务函数中使用上面的锁'''
def sale(rs):
    start = time.time()            # 程序启动时间
    with rs.pipeline() as p:
        '''
        通过管道方式进行连接
        多条命令执行结束，一次性获取结果
        '''

        while 1:
            lock = acquire_lock(rs, 'lock')
            if not lock: # 持锁失败
                continue

            #开始监测"lock"
            p.watch("lock")
            try:
                #开启事务
                p.multi()
                count = int(rs.get('apple')) # 取量
                p.set('apple', count-1)      # 减量
                # time.sleep(5)
                
                #提交事务
                p.execute()
                print('当前库存量: %s' % count)
                #成功则跳出循环
                break
            except:
                #事务失败对应处理
                print("修改数据失败")

            #无论成功与否最终都需要释放锁
            finally:

                res = release_lock(rs, 'lock', lock)
                #释放锁成功，
                if res:
                    print("删除锁成功")
                #释放锁失败，强制删除
                else:
                    print("删除锁失败,强制删除锁")
                    res = rs.delete('lock')
                    print(res)

        print('[time]: %.2f' % (time.time() - start))

rs = redis.Redis(host='127.0.0.1', port=6379)      # 连接redis
# rs.set('apple',1000)                             # # 首先在redis中设置某商品apple 对应数量value值为1000
sale(rs)

```

优化锁无法释放的问题，为锁添加过期时间

```python
def acquire_expire_lock(rs, lock_name, expire_time=10, locked_time=10):
    '''
    rs: 连接对象
    lock_name: 锁标识
    acquire_time: 过期超时时间
    locked_time: 锁的有效时间
    return -> False 获锁失败 or True 获锁成功
    '''
    identifier = str(uuid.uuid4())
    end = time.time() + expire_time
    while time.time() < end:
        # 当获取锁的行为超过有效时间，则退出循环，本次取锁失败，返回False
        if rs.setnx(lock_name, identifier): # 尝试取得锁
            # print('锁已设置: %s' % identifier)
            rs.expire(lock_name, locked_time)
            return identifier
        time.sleep(.001)
    return False
```









## 关于分布式锁

Watch：监测一个key。如果这个key的value改变，那个接下来的事务操作全部失效

multi:    开启一个事务。

Setnx:   跟set一样都往redis添加一个key。不一定的地方在于：set的时候如果这个值存在，就是修改操作。不存在就是添加操作。setnx：存在的时候不能再次添加，不存在的时候才能添加。



