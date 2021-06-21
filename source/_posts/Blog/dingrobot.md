---
title: "使用python3.7实现钉钉机器人群发"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-06-19 15:54"
cover : /img/timg.jpg
---

### 序幕

之前实现了[钉钉三方扫码登陆](https://www.sirxs.cn/2020/06/02/Blog/%E9%92%89%E9%92%89%E4%B8%89%E6%96%B9%E7%99%BB%E9%99%86/)，不得不说，钉钉还是一款很不错的办公软件。在最近的疫情期间，打游戏都不怎么坑了(因为钉钉的存在)。不过钉钉的群发机器人还是挺不错，可以自定义发送的信息。个人感觉比图灵好用，前期的[微信公众号添加机器人](https://www.sirxs.cn/2020/06/08/Live/%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7/)使用的就是图灵机器人。

不过关于钉钉机器人网上的一些攻略年代都比较久远，代码很多都基于python2，那我们尝试用python3.7来开发配置钉钉自定义机器人。

### 解决问题

#### quote_plus与quote的区别

```python
quote = urllib.parse.quote('a&b/c')
print('a&b/c:',quote)
plus = urllib.parse.quote_plus('a&b/c')
print('a&b/c:',plus)
```

结果(一个不编译/,一个编译)

```python
a&b/c: a%26b/c
a&b/c: a%26b%2Fc
```



### 创建机器人

1. 在创建机器人之前，我们需要有自己的钉钉号，和创建一个钉钉群聊，而且创建机器人不支持手机端，所以请在电脑端进行创建

2. 进入创建好的群，群成员可根据个人喜好添加，找到`智能群组手`

   ![dd](https://wangxs020202.gitee.io/images/note/dd1.png)

3. 进入之后，下拉找到`添加机器人`

   ![dd](https://wangxs020202.gitee.io/images/note/dd2.png)

4. 之后`添加机器人`，选择`自定义`

   ![dd](https://wangxs020202.gitee.io/images/note/dd3.png)

   ![dd](https://wangxs020202.gitee.io/images/note/dd4.png)

5. `机器人名称`自己修改（**密钥保存**）

   ![dd](https://wangxs020202.gitee.io/images/note/dd5.png)

   **需要注意的是，在安全设置一栏里，我们选择加签的方式来验证，在此说明一下，钉钉机器人的安全策略有三种，第一种是使用关键字，就是说你推送的消息里必须包含你创建机器人时定义的关键字，如果不包含就推送不了消息，第二种就是使用加密签名，第三种是定义几个ip源，非这些源的请求会被拒绝，综合来看还是第二种又安全又灵活。**

6. 点击`完成`，ok，群发机器人已创建好（**webhoot保存**）

   ![dd](https://wangxs020202.gitee.io/images/note/dd6.png)

   **值得一提的是，钉钉的机器人基于webhook协议，webhook呢是一个api概念,是微服务api的使用范式之一,也被成为反向api,即前端不主动发送请求。**

### 编写后端请求接口

[钉钉开发文档](https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq)

开发文档中居然出现了python3.8的代码，很遗憾我们用的是3.7的

```python
import time
import hmac
import hashlib
import base64
import urllib.parse

timestamp = str(round(time.time() * 1000))
secret = 'this is secret'
secret_enc = secret.encode('utf-8')
string_to_sign = '{}\n{}'.format(timestamp, secret)
string_to_sign_enc = string_to_sign.encode('utf-8')
hmac_code = hmac.new(secret_enc, string_to_sign_enc, digestmod=hashlib.sha256).digest()
sign = urllib.parse.quote_plus(base64.b64encode(hmac_code))
# print(timestamp)
# print(sign)


import requests,json   #导入依赖库
headers={'Content-Type': 'application/json'}   #定义数据类型
webhook = '"this is webhoot"&timestamp='+timestamp+"&sign="+sign
#定义要发送的数据
#"at": {"atMobiles": "['"+ mobile + "']"
#群发
data = {
    "msgtype": "text",
    "text": {"content": '群发消息'},
    "isAtAll": True
	}
#@
{
    "msgtype": "text", 
    "text": {
        "content": "群发消息"
    }, 
    "at": {
        "atMobiles": [
            "156xxxx8827", 
            "189xxxx8325"
        ], 
        "isAtAll": False
    }
}
res = requests.post(webhook, data=json.dumps(data), headers=headers)   #发送post请求

print(res.text)
```

推送效果

![dd](https://wangxs020202.gitee.io/images/note/dd7.png)

**校验不通过的消息将会发送失败，错误如下：**

```json
// 消息内容中不包含任何关键词
{
  "errcode":310000,
  "errmsg":"keywords not in content"
}

// timestamp 无效
{
  "errcode":310000,
  "errmsg":"invalid timestamp"
}

// 签名不匹配
{
  "errcode":310000,
  "errmsg":"sign not match"
}

// IP地址不在白名单
{
  "errcode":310000,
  "errmsg":"ip X.X.X.X not in whitelist"
}
```