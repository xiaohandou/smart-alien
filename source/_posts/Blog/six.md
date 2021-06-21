---
title: "字典之keys()、values()和 items()方法"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-04-08"
cover : /img/timg.jpg
---



## 字典有3个方法，他们返回类似的列表值，分别对应于字典的键，值和键值对：keys()、values()和item()。

## 这些方法返回的值不是真正的列表，它们不能被修改，没有append()方法。但这些数据类型可以用于for循环。

values()方法

```
spam = {'color':'red','age':18}

for item in spam.values(): 

    print(item)  # red 18  取的是值
```

keys()方法

```
spam = {'color':'red','age':18}

for item2 in spam.keys():    

    print(item2)  # color age  取的时键
```

items()方法 这个方法最特别

```
单个参数

spam = {'color':'red','age':18}

for item1 in spam.items():
    print(item1)  # ('color', 'red')('age', 18) items 元组的形式  取出键 值对
    
两个参数

spam = {'color':'red','age':18}

for k,v in spam.items():   

    print(k,v)   # color red age 18   若有两个参数则将key 与 value 单独去取
```