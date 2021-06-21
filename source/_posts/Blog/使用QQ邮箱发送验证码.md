---

title: "使用QQ邮箱发送验证码"

tags: ["Python"]

categories: ["编程 · 技术"]

date: "2020-05-27"

cover : /img/timg.jpg

---

Python3实现发送邮件验证码功能

这个其实非常简单

首先我们需要有一个QQ邮箱，然后进入QQ邮箱，点击设置

![img](https://wangxs020202.gitee.io/images/me/QQem1.png)

点击账户，找到**POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务**，并将图片中的服务开启

![img](https://wangxs020202.gitee.io/images/me/QQem2.png)

![img](https://wangxs020202.gitee.io/images/me/QQem3.png)

之后就会获取到一个QQ邮箱获取授权码

之后进入我们的django中，在setting中配置以下代码

```python
EMAIL_USE_SSL = True  # Secure Sockets Layer 安全套接层, 取决于邮件服务器是否开启加密协议
EMAIL_HOST = 'smtp.qq.com'  # 邮件服务器地址 ， 如果是163改成smtp.163.com
EMAIL_PORT = 465  # 邮件服务器端口
EMAIL_HOST_USER = 'xxxxxxxxxxx@qq.com'  # 登陆邮件服务器的账号
EMAIL_HOST_PASSWORD = 'xxxxxxxxxxx'  # 登陆邮件服务器的授权密码
DEFAULT_FROM_EMAIL = EMAIL_HOST_USER  # 邮件的发送者
# # 允许哪些人可以请求你和请求方式
CORS_ORIGIN_ALLOW_ALL = True  # 改为true是所有人都可以访问我
```



之后在我们需要使用的文件中导入

```python
from django.core.mail import send_mail  # 用来发送用
from django_test.settings import DEFAULT_FROM_EMAIL
from django_test.settings import EMAIL_HOST_USER
```



写一个随机验证码demo

```python
# 生成随机验证码
def get_random_str():
    # 验证码是由 字母a~z  A~Z 数字 0~9 组成
    # 在 ascii 码中 小写a的起点是97 大写A起点是65
    a_ = [chr(var) for var in range(97, 123)]  # 小写a的列表推导式
    A_ = [chr(var) for var in range(65, 91)]  # 大写A的列表推导式
    num_ = [str(var) for var in range(0, 9)]  # 数字的
    # 使用sample 在列表中随机生成6个任意字母数字
    return ''.join(random.sample(a_ + A_ + num_, 6))
```





接下来就是发送邮件的代码

```python
class EmailVerify(APIView):
    def post(self, request):
        email = request.data.get('email')  # 接收到用户发来的邮箱
        token = get_random_str()  # 生成验证码
        print(token)
        subject = '通过邮箱找回密码！！'
        message = '你的验证码是:%s' % token
        send_mail(subject, message, DEFAULT_FROM_EMAIL,
                  [email])  # 把邮件发过去
        res['code'] = 200
        res['message'] = "验证码已发送"
        return JsonResponse(res)
```



这样就可以实现QQ邮箱发送验证码了