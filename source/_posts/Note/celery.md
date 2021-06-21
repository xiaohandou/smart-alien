---
title: "Celery"
tags: ["Django","Celery","Redis"]
categories: ["总结 · 笔记"]
date: "2020-05-30"
cover : /img/notepic.jpg
---



### 一、什么是Celery

Celery的框架图：

![celery](https://wangxs020202.gitee.io/images/me/celery.png)

Celery是一个专注于实时处理和任务调度的分布式任务队列。所谓任务就是消息，消息中的有效载荷中包含要执行任务需要的全部数据。

在程序的运行过程中，我们经常会碰到一些耗时耗资源的操作，为了避免它们阻塞主程序的运行，我们经常会采用多线程或异步任务。

通常的使用场景：

- Web开发中。当一个操作需要很长时间才能执行完成是，可以用celery去异步处理
- 定时任务。Celery可以帮助我们快速在不同的机器设定不同种任务。
- 同时完成多项工作。

### 二、为什么要用Celery

Celery其特性是：

- 简单。Celery 易于使用和维护，并且它 *不需要配置文件* 。

  Celery 有一个活跃、友好的社区来让你寻求帮助，包括一个 邮件列表 和一个 IRC 频道 。

  下面是一个你可以实现的最简应用：

  ```python
  from celery.task import task
  # 自定义要执行的task任务
  @task
  def print_test():
  	print("nict try")
  	return 'hello'
  ```

- 可用性高。若连接丢失或失败，会自动连接。并且通过主/从方式复制提高可用性

- 快速。单个celery进程可以在短时间完成上百万任务。

- 灵活。所有celery部分都可以拓展和单独使用

### 三、缺点
Celery很好用，可以快速完成任务，对用户的体验是特别好的。但是，任何事物都是两面性的。它的缺点就是会增加Redis的缓存量，每次请求都会将数据存入Redis里面。而且依赖eventlet的库

使用我们要定期清理自己的Redis数据库

```python
redis-cli
flushall 
```

注：flushall会清空所有数据库数据；flushdb只清空当前数据库数据

### 四、怎样用Celery

1、安装对应的库

```python
pip3 install celery==4.4.2
pip3 install eventlet==0.25.2
pip3 install Django==2.0.4
```

2、配置settings文件：

```python
#redis
CELERY_BROKER_URL = 'redis://localhost:6379/'
#broker配置redis
CELERY_RESULT_BACKEND = 'redis://localhost:6379/'
#文件格式为：json
CELERY_RESULT_SERIALIZER = 'json'
```

3、在settings文件同级目录创建celery.py

```python
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

# 设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'Django项目名称.settings')

# 注册Celery的APP
app = Celery('Django项目名称')
# 绑定配置文件
app.config_from_object('django.conf:settings', namespace='CELERY')

# 自动发现各个app下的tasks.py文件
app.autodiscover_tasks()

#如果有需要可以将该任务设置成定时任务
from celery.schedules import crontab
CELERY_BEAT_SCHEDULE = {
    # 周期性任务
    'task-one': {
        'task': 'myapp.tasks.print_test',
        'schedule': 周期时间,
        # 'args': ()
    }
}
```

4、修改settings文件同级目录的init.py文件

```python
from __future__ import absolute_import, unicode_literals
from .celery import app as celery_app

#导包
import pymysql
#初始化
pymysql.install_as_MySQLdb()



__all__ = ['celery_app']
```

5、在应用中创建tasks.py文件

```python
from celery.task import task

# 自定义要执行的task任务
@task
def print_test():
	print("nict try")
	return 'hello'
```

6、在视图页面进行调用

```python
from myapp import tasks

def ctest(request,*args,**kwargs):
    res=tasks.print_test.delay()
    #delay方法就是异步方式请求
    #任务逻辑
    return JsonResponse({'status':'successful','task_id':res.task_id})
```

7、 在manage.py的目录下启动celery服务

```python
celery worker -A mydjango -l info -P eventlet
```

8、 在浏览器中调用异步服务接口

![celery](https://wangxs020202.gitee.io/images/me/celery1.png)

 同时也可以在backend中查询任务结果

![celery](https://wangxs020202.gitee.io/images/me/celery2.png)

*****注：redis中的key并不是单纯的task_id，而是需要加上前缀celery-task-meta-

 9、最后，如果需要启动定时任务，就需要在manage.py所在的文件夹内单独启动beat服务

```python
celery -A mydjango beat -l info
```

