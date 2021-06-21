---
title: "OOP总结"
tags: ["Python"]
categories: ["总结 · 笔记"]
date: "2020-06-17 13:54:10"
cover : /img/notepic.jpg
---

Python 是一种解释型、面向对象、的高级程序设计语言。

那什么是面向对象呢

与面向对象经常拿来对比的就是面向过程编程，那么他们之间的区别在什么地方呢？

打个比方 ，我们买过的一般的玩具（变形金刚），我们必须要按照它说明书上的步骤，一步一步的去组装，才能得到最后的玩具，如果我们想要一个新的玩具，就要去商场买一个新的，然后按照说明书的顺序一步一步的组装。这就是面向过程

而面型对象呢？就可以理解为积木，没有一个固定的拼装方式，我们可以发挥自己的想象力，去自由的拼装和组装，同样的模块在不同的地方可以起到不同的作用（多态），一块儿积木就是一个最小的单位，我们哪里需要就放到哪里（封装）。也可以用多个对象组装起来去拼装成一个新的对象（继承）。大大的方便了我们的设计，不再拘泥于过程，极大程度上的放飞了生产力和效率。

总的来说就是`封装`,`继承`,`多态`，`抽象`

封装，顾名思义就是将内容封装到某个地方，以后再去调用被封装在某处的内容。

所以，在使用面向对象的封装特性时，需要：

- 将内容封装到某处
- 从某处调用被封装的内容

一、将内容封装到某处

```python
# 创建类
class Foo:
    #创建并初始化它的属性
	def __init__(self,name,age):
		self.name = name
		self.age = age

# 根据类Foo创建对象
# 自动执行Foo类的__init__方法
obj1 = Foo('老王',18)
# 将老王与18分别封装到obj1的name与age属性中
obj2 = Foo('老刘',28)
# 将老刘与28分别封装到obj2的name与age属性中
```

二、从某处调用被封装的内容

调用被封装的内容时，有两种情况：

- 通过对象直接调用
- 通过self间接调用

1、通过对象直接调用被封装的内容

```python
# 创建类
class Foo:
	def __init__(self,name,age):
		self.name = name
		self.age = age

# 根据类Foo创建对象
# 自动执行Foo类的__init__方法
obj1 = Foo('老王',18)
# 将老王与18分别封装到obj1的name与age属性中
print(obj1.name) # 直接调用obj1对象的name属性
print(obj1.age) # 直接调用obj1对象的age属性
```

2、通过self间接调用被封装的内容

```python
# 创建类
class Foo:
	def __init__(self,name,age):
		self.name = name
		self.age = age

	def detail(self):
		print(self.name)
		print(self.age)

# 根据类Foo创建对象
# 自动执行Foo类的__init__方法
obj1 = Foo('老王',18)
# 将老王与18分别封装到obj1的name与age属性中
obj1.detail() # Python默认会将obj1传给self参数，即：obj1.detail(obj1)，所以，此时方法内部的 self ＝ obj1，即：self.name 是 wupeiqi ；self.age 是 18
```

**综上所述，对于面向对象的封装来说，其实就是使用构造方法将内容封装到 对象 中，然后通过对象直接或者self间接获取被封装的内容。**

继承，面向对象中的继承和现实生活中的继承相同，即：子可以继承父的内容。

例如：

　　老王可以：吃、喝、奥利给

　　老刘可以：吃、喝、奥利给

如果我们要分别为老王和老刘创建一个类，那么就需要为 老王 和 老刘 实现他们所有的功能，如下所示：

```python
# 定义父类
class Foo:
	def eat(self):
		print('%s在吃'%self.name)
	def drink(self):
		print('%s在喝'%self.name)

# 定义子类并继承父类
class LaoWang(Foo):
	def __init__(self,name):
		self.name = name

	def cry(self):
		print('%s奥利给'%self.name)

# 定义子类并继承父类
class LaoLiu(Foo):
	def __init__(self,name):
		self.name = name

	def cry(self):
		print('%s奥利给'%self.name)

c1 = LaoWang('老王')
# 调用父类方法
c1.eat()
c1.drink()
# 调用自身方法
c1.cry()
print('===================')
c2 = LaoLiu('老刘')
# 调用父类方法
c2.eat()
c2.drink()
# 调用自身方法
c2.cry()

```

多态，字面理解为多种形态，没错，就是一个方法能表现出不同的形态。

同一**方法**作用于不同的对象，可以有不同的解释，产生不同的结果。

现实中多态的例子举不胜举，比如我按下回车键，如果在word中就是换行，如果在QQ的发送消息界面就是发送消息，如果在命令行界面就是执行命令等等。

```python
class Person:
    def __init__(self,name,sex):
        self.name = name
        self.sex = sex

    def print_title(self):
        if self.sex == "男士":
            print("%s是man"%self.name)
        elif self.sex == "女士":
            print("%s是woman"%self.name)

class Child(Person):# Child 继承 Person
    def print_title(self):
        if self.sex == "男士":
            print("%s是boy"%self.name)
        elif self.sex == "女士":
            print("%sgirl"%self.name)

老王 = Child("老王","男士")
小红 = Person("小红","女士")

老王.print_title()
小红.print_title()
```

抽象：

提取现实世界中某事物的关键特性，为该事物构建模型的过程。对同一事物在不同的需求下，需要提取的特性可能不一样。得到的抽象模型中一般包含：**属性（数据）和操作（行为）**。这个抽象模型我们称之为**类**。对类进行实例化得到对象。

抽象——就是忽略一个主题中与当前目标无关的那些方面，以便更充分地注意与当前目标有关的方面。(就是把现实世界中的某一类东西，提取出来，用程序代码表示，抽象出来一般叫做类或者接口。)抽象并不打算了解全部问题，而只是选择其中的一部分，暂时不用部分细节。抽象包括两个方面，一是数据抽象，二是过程抽象。

数据抽象——就是用代码的形式表示现时世界中一类事物的特性，就是针对对象的属性。比如建立一个鸟这样的类，鸟都有以下属性：一对翅膀、两只脚、羽毛等。抽象出来的类都是鸟的属性，或者成员变量。

过程抽象——就是用代码形式表示现实世界中事物的一系列行为，就是针对对象的行为特征。比如鸟会飞、会叫等。抽象出来的类一般都是鸟的方法。

