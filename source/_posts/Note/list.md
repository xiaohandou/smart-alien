---
title: "Python之列表(list)"
tags: ["Python"]
categories: ["总结 · 笔记"]
date: "2020-05-07"
cover : /img/notepic.jpg
---

1. append用于在列表末尾追加新的对象

```
a = [1,2,3]
a.append(4)                          #the result ： [1, 2, 3, 4]
```

2. count方法统计某个元素在列表中出现的次数
```
a = ['aa','bb','cc','aa','aa']
print(a.count('aa'))                 #the result ： 3
```

3. extend方法可以在列表的末尾一次性追加另一个序列中的多个值

```
a = [1,2,3]
b = [4,5,6]
a.extend(b)                          #the result ：[1, 2, 3, 4, 5, 6]
```

4. index函数用于从列表中找出某个值第一个匹配项的索引位置
```
a = [1,2,3,1]
print(a.index(1))                   #the result ： 0
```

5. insert方法用于将对象插入到列表中
```
a = [1,2,3]
a.insert(0,'aa')            #the result : ['aa', 1, 2, 3]
```

6. pop方法会移除列表中的一个元素（默认是最后一个），并且返回该元素的值
```
a = [1,2,3]
a.pop()                             #the result ： [1, 2]
a.pop(0)
```

7. remove方法用于移除列表中某个值的第一个匹配项
```
a = ['aa','bb','cc','aa']
a.remove('aa')                      #the result ： ['bb', 'cc', 'aa']
```

8. reverse方法将列表中的元素反向存放
```
a = ['a','b','c']
a.reverse()                         #the result ： ['c', 'b', 'a']
```

9. sort方法用于在原位置对列表进行排序，意味着改变原来的列表，让其中的元素按一定顺序排列
```
a = ['a','b','c',1,2,3]
a.sort()                           #the result ：[1, 2, 3, 'a', 'b', 'c']
```

10. enumrate
```
li = [11,22,33]
for k,v in enumerate(li, 1):
    print(k,v)
```