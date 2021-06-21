---
title: "联合索引的意义，作用，以及使用方式（ORM）"
tags: ["Django"]
categories: ["总结 · 笔记"]
date: "2020-05-19"
cover : /img/notepic.jpg
---

### 一、为什么要用联合索引

1、每多创建一个索引，都会增加操作的开销和磁盘空间的开销。对于大量数据的表，这可是不小的开销。

2、覆盖索引。数据库可以直接通过遍历索引取得数据，而无需回表，这减少了很多的随机io操作。减少io操作，特别的随机io其实是dba主要的优化策略。所以，在真正的实际应用中，覆盖索引是主要的提升性能的优化手段之一。

3、索引列越多，通过索引筛选出的数据越少。如果是复合索引，通过索引筛选出1000w *10% *10% *10%=1w，然后再排序、分页，哪个更高效，一眼便知。

### 二、联合索引的使用

```
class User(models.Model):
    #一.常用字段：

    #1.字符字段
    username = models.CharField(max_length=32)

    #2.数字字段
    age = models.IntegerField()#整数
    num = models.DecimalField(max_digits=10,decimal_places=2)#小数，长度10，小数点位数2

    #3.时间字段
    ctime = models.DateTimeField()
    # 时间字段通过models.User.objects.create(ctime='2020-4-29')来添加数据

    #二.常用参数：
    null = True
    default = xx
    max_length = 32
    db_index = True #普通索引
    unique = True #唯一索引

    #class Meta是固定写法，并且必须写在class User里面，只要写在它里面就可以起作用。
    class Meta:
        #联合唯一索引
        unique_together = (
            ('username','age'),
        )
        #联合索引(不唯一)
        index_together = (
            ('username', 'age'),
        )
```

### 三、注意

1、联合索引不要以主键开头，不然联合索引和主键索引作用是一样的

2、当你的查询sql where条件中用到的多个字段在联合索引中的查询速度优于在单列索引的速度

3、使用联合索引时，当你的where条件中不包含联合索引中的第一个字段时，无法用到索引

4、根据最左原则，联合索引可以使用部分生效的索引。(a,b,c) 当 where a=1 and c=2 a 索引是生效的

5、单列索引不受字段最左原则限制,但受内容最左原则限制