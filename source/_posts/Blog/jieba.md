---
title: "Python中文分词 jieba"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-06-04 13:59:18"
cover : /img/timg.jpg
---

### 序幕

Python有个模块可以将一段话中的关键词提取出来，支持中文简体，繁体分词，还支持自定义词库。 --它就是Python中文分词组件**jieba**

`jieba` 支持三种分词模式：精确模式、全模式和搜索引擎模式，下面是三种模式的特点。

精确模式：试图将语句最精确的切分，不存在冗余数据，适合做文本分析

全模式：将语句中所有可能是词的词语都切分出来，速度很快，但是存在冗余数据

搜索引擎模式：在精确模式的基础上，对长词再次进行切分

### jieba

1. #### 安装

   因为 `jieba` 是一个第三方库，所有需要我们在本地进行安装。

   Windows 下使用命令安装：在联网状态下，在命令行下输入 `pip install jieba` 进行安装，安装完成后会提示安装成功

   在 pyCharm 中安装：打开 `settings`，搜索 `Project Interpreter`，在右边的窗口选择 `+` 号，点击后在搜索框搜索 `jieba`，点击安装即可

2. #### 三种模式使用

   ```python
   import jieba
   seg_str = '好好学习，天天向上。'
   
   print("/".join(jieba.lcut(seg_str)))    # 精简模式，返回一个列表类型的结果
   print("/".join(jieba.lcut(seg_str, cut_all=True)))      # 全模式，使用 'cut_all=True' 指定 
   print("/".join(jieba.lcut_for_search(seg_str)))     # 搜索引擎模式
   ```

   ![gitee](https://wangxs020202.gitee.io/images/note/jieba.png)
   
3. #### jieba.analyse的使用

   ```python
   import jieba.analyse
   
   data = 'Python是一种跨平台的计算机程序设计语言。 Python是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。最初被设计用于编写自动化脚本(shell)，随着版本的不断更新和语言新功能的添加，越多被用于独立的、大型项目的开发。'
   
   # 提取标签(关键词，权重) topK(权重前五)  withWeight(使用权重)
   for keyword,weight in jieba.analyse.extract_tags(data,withWeight=True,topK=5):
   	print('%s-%s'%(keyword,weight))
   ```

   ![gitee](https://wangxs020202.gitee.io/images/note/jieba1.png)

   

### 总结

jieba特点

1. 精确模式，试图将句子最精确地切开，适合文本分析；
2. 全模式，把句子中所有的可以成词的词语都扫描出来, 速度非常快，但是不能解决歧义；
3. 搜索引擎模式，在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词。
4. 支持繁体分词
5. 支持自定义词典
6. MIT 授权协议

​	







