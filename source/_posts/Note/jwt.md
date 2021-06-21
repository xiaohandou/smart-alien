---
title: "JWT"
tags: ["JWT","Python"]
categories: ["总结 · 笔记"]
date: "2020-05-31"
cover : /img/notepic.jpg
---

## JWT是什么

1. Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。

2. 组成:

    header.payload.signature 头.载荷.签名

3. 介绍：

   ​	①、 header：一般存放如何处理token的方式：声明类型，声明加密的算法 

   ​	②、payload：数据的主体部分：用户信息、发行者、过期时间等

   ​	③、 signature：签名：这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，	然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。

4. 将这三部分连接成一个完整的jwt：

   ```python
   eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6Im93ZW4iLCJleHAiOjE1NTgzMDM1NDR9.4j5QypLwufjpqoScwUB9LYiuhYcTw1y4dPrvnv7DUyo
   ```

### 工作原理

1. jwt = base64(头部).base64(载荷).hash256(base64(头部).base(载荷).密钥)
2. base64是可逆的算法、hash256是不可逆的算法
3. 密钥是固定的字符串，保存在服务器

## 为什么要用JWT

1. 传统的session认证：

   当用户登陆时，登录信息会在响应时传递给浏览器，告诉其保存为cookie,以便下次请求时发送给我们的应用，这样我们的应用就能识别请求来自哪个用户了,这就是传统的基于session认证。但是这种基于session的认证使应用本身很难得到扩展，随着不同客户端用户的增加，独立的服务器已无法承载更多的用户，而这时候基于session认证应用的问题就会暴露出来.

2. 基于token的鉴权机制：

   基于token的鉴权机制类似于http协议也是无状态的，它不需要在服务端去保留用户的认证信息或者会话信息。这就意味着基于token认证机制的应用不需要去考虑用户在哪一台服务器登录了，这就为应用的扩展提供了便利。

## 如何使用JWT

[官方文档](https://github.com/jpadilla/django-rest-framework-jwt)

### 安装子虚拟环境

```python
pip install djangorestframework-jwt
```

### 测试

### 使用：urls.py

```python
from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token
urlpatterns = [
    path('login/', obtain_jwt_token),
]
```

### 测试接口：POST请求

postman发送post请求

```python
接口：http://api.luffy.cn:8000/user/login/

数据：
{
    "username":"admin",
    "password":"admin"
}
```

### 开发

### 配置信息：settings.py

```python
import datetime
JWT_AUTH = {
    'JWT_AUTH_HEADER_PREFIX': 'JWT',
    # 过期时间
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    # 自定义认证结果：见下方序列化user和自定义response
    'JWT_RESPONSE_PAYLOAD_HANDLER': 'user.utils.jwt_response_payload_handler',  
}
```

### 序列化USER：serializers.py(自己创建)

```python
from rest_framework import serializers
from .models import User
class UserModelSerializer(serializers.ModelSerializer):
    """轮播图序列化器"""
    class Meta:
        model = User
        fields = ["username", "mobile"]
```

### 自定义response：user.py

```python
from .serializers import UserModelSerializers
def jwt_response_payload_handler(token, user=None, request=None):
    return {
        'token': token,
        'user': UserModelSerializer(user).data
    }
    # restful 规范
    # return {
    #     'status': 0,
    #     'msg': 'OK',
    #     'data': {
    #         'token': token,
    #         'username': user.username
    #     }
    # }
```

### 在登陆的时候使用：user.py

```python
# 生成jwt
jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER
payload = jwt_payload_handler(user)
token = jwt_encode_handler(payload)
```

### 解析token中的数据：user.py

```python
#获取token
jwt_token = request.GET.get("token", None)
#解析数据
user_json = jwt_decode_handler(jwt_token)
# 获取token中的uid
user_id = user_json.get("user_id")
```

### 多方式登录：user.py

```python
from django.contrib.auth.backends import ModelBackend
from .models import User
import re
class JWTModelBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        """
        :param request:
        :param username: 前台传入的用户名
        :param password: 前台传入的密码
        :param kwargs:
        :return:
        """
        try:
            if re.match(r'^1[3-9]\d{9}$', username):
                user = User.objects.get(mobile=username)
            elif re.match(r'.*@.*', username):
                user = User.objects.get(email=username)
            else:
                user = User.objects.get(username=username)
        except User.DoesNotExist:
            return None  # 认证失败就返回None即可，jwt就无法删除token
        # 用户存在，密码校验通过，是活着的用户 is_active字段为1
        if user and user.check_password(password) and self.user_can_authenticate(user):
            return user  # 认证通过返回用户，交给jwt生成token
```

### 配置多方式登录：settings.py

```python
AUTHENTICATION_BACKENDS = ['user.JWTModelBackend']
```

## 总结

### 优点

1.因为json的通用性，所以JWT是可以进行跨语言支持的，像JAVA,JavaScript,NodeJS,PHP等很多语言都可以使用。

2.因为有了payload部分，所以JWT可以在自身存储一些其他业务逻辑所必要的非敏感信息。

3.便于传输，jwt的构成非常简单，字节占用很小，所以它是非常便于传输的。

4.它不需要在服务端保存会话信息, 所以它易于应用的扩展

### 安全相关

1.不应该在jwt的payload部分存放敏感信息，因为该部分是客户端可解密的部分。

2.保护好secret私钥，该私钥非常重要。

3.如果可以，请使用https协议