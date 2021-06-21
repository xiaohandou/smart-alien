---
title: "Celery_Error"
tags: ["Django","Celery"]
categories: ["总结 · 笔记"]
date: "2020-06-01"
cover : /img/notepic.jpg
---

### 一、错误
前几天给Django配置了Celery,使用

```python
celery worker -A celery_task -l info -P eventlet
```
启动Celery,没有使用POST方法测试

今天测试使用了下POST，当我启动服务访问时：

![error](https://wangxs020202.gitee.io/images/me/celeryerror.png)

```python
TypeError: wrap_socket() got an unexpected keyword argument '_context'
```

出现了错误

### 二、解决

requests包的requests.post发送后，传不回数据

所以，在改变服务器启动方法不要用eventlet，加个参数

```python
celery worker -A celery_task -l info -P=solo
```