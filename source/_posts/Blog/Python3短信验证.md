---
title: "Python3短信验证"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-05-28"
cover : /img/timg.jpg
---

短信服务验证服务已经不是什么新鲜事了，但是免费的手机短信服务却不多见，本次利用Python3.0基于阿里云服务和腾讯云服务分别来体验一下国际短信和国内短信接口。

### 一、阿里云短信服务

首先是阿里云，注册：https://www.aliyun.com/

短信服务：https://www.aliyun.com/product/sms?spm=5176.10695662.1128094.2.2a6b4bee30Yrlc

搜索*短信服务*，注册成功后可以免费领取（注意：这个只有中午10点以后才开放，而且每人只有100条短信，超出会扣钱的）

![ali](https://wangxs020202.gitee.io/images/me/ali1.jpg)

之后找到*国内短信，标签管理*，并*添加标签*

![ali](https://wangxs020202.gitee.io/images/me/ali2.jpg)

进入添加签名后*使用场景*选择验证码（注意：这个验证码用户级的只能创建一个，且用且珍惜）

![ali](https://wangxs020202.gitee.io/images/me/ali3.jpg)

签名添加完毕后，退出添加模板

![ali](https://wangxs020202.gitee.io/images/me/ali4.jpg)

模板添加完成之后阿里云上基本以操作完毕。之后点击个人头像，找到*AccessKey管理*

![ali](https://wangxs020202.gitee.io/images/me/ali5.jpg)

进入之后会有一个*用户*AccessKey*，如果没有可以自行创建

![ali](https://wangxs020202.gitee.io/images/me/ali6.jpg)

完成之后找到*快速学习*，这里可以查看API Demo，里面可以查看文档和测试短信发送

![ali](https://wangxs020202.gitee.io/images/me/ali7.jpg)

这些完成之后就差不多了。。。。

接下里安装阿里云短信的sdk

```python
aliyun-python-sdk-core
```

编写脚本，并测试

```python
#将需要的包导入
from aliyunsdkcore.client import AcsClient
from aliyunsdkcore.request import CommonRequest

#脚本
#个人的appid和appkey以及地区
client = AcsClient('<accessKeyId>', '<accessSecret>', 'cn-hangzhou')
# 创建连接
request = CommonRequest()
# 设置接收格式
request.set_accept_format('json')
# 设置域（阿里云的域名）
request.set_domain('dysmsapi.aliyuncs.com')
# 设置发送方式
request.set_method('POST')
# 设置协议类型
request.set_protocol_type('https') # https | http
# 设置版本
request.set_version('2017-05-25')
# 设置操作名称
request.set_action_name('SendSms')

request.add_query_param('RegionId', "cn-hangzhou") #地区
request.add_query_param('PhoneNumbers', "接收手机号") # 三方接收手机号
request.add_query_param('SignName', "标签名称") # 创建的标签名称
request.add_query_param('TemplateCode', " 模版CODE") # 创建的模板code
request.add_query_param('TemplateParam', "{‘code’:}") #模板参数
#发送请求
response = client.do_action(request)
# python2:  print(response) 
print(str(response, encoding = 'utf-8'))
```

### 二、腾讯云短信服务

第一步，注册腾讯云  https://cloud.tencent.com

（注意：注册时有点复杂）

注册成功之后搜索*短信*，在这个我们需要进行一系列的认证操作

认证成功之后点击*应用管理*找到*应用列表*，之后你就会看到有一个默认应用，但是你也可以自己创建一个

![ali](https://wangxs020202.gitee.io/images/me/txun1.png)

创建完成之后查看自己的APPID与appkey，并保存

![ali](https://wangxs020202.gitee.io/images/me/txun2.png)

之后找到*国内短信*并创建自己的签名（注意：这个个人需要小程序）

![ali](https://wangxs020202.gitee.io/images/me/txun3.png)

签名创建之后，创建模板

![ali](https://wangxs020202.gitee.io/images/me/txun4.png)

接下里安装腾讯云短信的sdk

```python
pip3 install qcloudsms_py
```

编写脚本测试

```python
# 短信应用SDK AppID
appid = 你的appid  # SDK AppID是1400开头

# 短信应用SDK AppKey
appkey = "你的appkey"

# 需要发送短信的手机号码
phone_numbers = ["你要发送的手机号"]

# 短信模板ID，需要在短信应用中申请
template_id = 在模板列表里获取  

# 签名
sms_sign = "签名名称"

from qcloudsms_py import SmsSingleSender
from qcloudsms_py.httpclient import HTTPError


import ssl
ssl._create_default_https_context = ssl._create_unverified_context

ssender = SmsSingleSender(appid, appkey)
params = ["6666","5"]  # 当模板没有参数时，`params = []`
try:
    result = ssender.send_with_param(86, phone_numbers[0],
        template_id, params, sign=sms_sign, extend="", ext="")  # 签名参数不允许为空串
    print(result)
except HTTPError as e:
    print(e)
except Exception as e:
    print(e)
```

