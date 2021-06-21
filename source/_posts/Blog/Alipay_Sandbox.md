---
title: "（支付宝模拟支付）使用python3.7+Vue.js2.0+Django2.0.4实现支付宝模拟支付功能"
tags: ["Django","Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-06-13 15:54"
cover : /img/timg.jpg
---

### 序幕

在当今科技发达的时代，纸币已将慢慢的从人们的口袋消失。随着带来的是更方便的电子货币（手机虚拟货币）。[支付宝](https://baike.baidu.com/item/%E6%94%AF%E4%BB%98%E5%AE%9D/496859)，是我国比较强大的第三方支付平台，也被广大人群所喜爱。在当今的基本所有网上购物平台都支持支付宝支付，所以我们用支付宝沙箱环境来模拟实现三方支付

#### 什么是沙箱环境

蚂蚁沙箱环境 (Beta) 是协助开发者进行接口功能开发及主要功能联调的辅助环境。沙箱环境模拟了开放平台部分产品的主要功能和主要逻辑（当前沙箱支持产品请参考下文的 **沙箱支持产品** 列表）。 在开发者应用上线审核前，开发者可以根据自身需求，先在沙箱环境中了解、组合和调试各种开放接口，进行开发调通工作，从而帮助开发者在应用上线审核完成后，能更快速、更顺利的进行线上调试和验收工作。 如何使用和配置沙箱环境请参考下文 **如何使用沙箱环境**。

**注意**

- 由于沙箱为模拟环境，在沙箱完成接口开发及主要功能调试后，请务必在蚂蚁正式环境进行完整的功能验收测试。所有返回码及业务逻辑以正式环境为准。
- 为保证沙箱稳定，沙箱环境测试数据会进行定期数据清理。Beta 测试阶段每周日中午12点至每周一中午12点为维护时间，在此时间内沙箱环境部分功能可能会不可用，敬请谅解。
- 请勿在沙箱进行压力测试，以免触发相应的限流措施，导致无法正常使用沙箱环境。
- 沙箱支持的各个开放产品，沙箱使用的特别说明请参考各产品的快速接入文档或技术接入文档章节。

### 如何使用沙箱环境

#### 第一步配置沙箱应用环境

1. 进入 **开放平台 > 开发者中心** 在开发服务选项中点击 **研发服务** 即可进入 [沙箱环境](https://openhome.alipay.com/platform/appDaily.htm)。

   ![alipay](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/254687/1589449249412-2fc126a6-4ae8-466d-8b94-be85ca191dc4.png)

2. 进入沙箱环境页面，系统已经自动为你创建一个应用，在 **信息配置** 中可以看到应用信息。

   ![alipay](https://static.dingtalk.com/media/lALOnH_0fM0BHM0Cog_674_284.png)

   1. 沙箱环境密钥设置支持上传 RSA2(SHA256) 的应用公钥和公钥证书两种形式，详见 [生成RSA密钥](https://opendocs.alipay.com/open/291/105971)。配置 RSA2(SHA256) 的应用公钥后，不需要配置 RSA(SHA1) 密钥，签名算法区别参见[ RSA 和RSA2 签名算法区别](https://opendocs.alipay.com/open/291/106115)。配置 crs 公钥证书时 **组织/公司** 需填写为 **沙箱环境**；
   2. 编写代码时，请将：

   - 请求网关修改为：`https://openapi.alipaydev.com/gateway.do`
   - appid 切换为沙箱的 appid
   - 签名方式使用 RSA2
   - 应用私钥（private_key）使用第 1 步生成的 RSA2 (SHA256) 的私钥(请根据开发语言进行选择原始或 pkcs8 格式)。
   - 支付宝公钥（public_key）切换为第 1 步配置后应用公钥后，点击查看支付宝公钥看到的公钥。

   ![alipay](https://static.dingtalk.com/media/lALOsWs3ic0Bus0CNw_567_442.png_620x10000q90g.jpg)

   选看部分作为进阶使用，非必填项；

   1. 应用网关：该地址用于接收开放平台的异步通知。目前沙箱环境不需要配置此参数；
   2. 授权回调地址；第三方应用授权或获取用户信息中用于接收授权回调信息的地址。使用相关产品时需进行配置：

   - 第三方应用授权：授权 url 中的 redirect_uri 必须与此值相同。
   - 获取用户信息：授权 url 中的 redirect_uri 的域名必须与此值相同(例如：授权回调地址配置：[https://auth.example.com/authCallBack](https://auth.example.com/authCallBack) 高亮部分需和授权url相同)。

   1. AES 密钥：目前不再使用。

#### 第二步根据文档写代码

#### 电脑网站支付流程

![alipay](https://wangxs020202.gitee.io/images/note/电脑网站支付流程图.png)

#### 后端接口设计

**请求方式**： GET 

**请求参数**： 路径参数

| 参数  | 类型 | 是否必须 | 说明     |
| ----- | ---- | -------- | -------- |
| order | str  | 是       | 订单编号 |
| price | int  | 是       | 订单价格 |

**返回数据**： JSON

| 返回值     | 类型 | 是否必须 | 说明           |
| ---------- | ---- | -------- | -------------- |
| alipay_url | str  | 是       | 支付宝支付链接 |

#### 后端实现（支付请求接口）

```html
from alipay import AliPay
import datetime

class Alipay(APIView):
    def get(self, request):
         # 随机生成订单号
        order = datetime.datetime.now().strftime("%Y%m%d%H%M%S") + str(random.randint(10, 99))
        # 获取支付价格
        price = request.GET.get("price", None)
        # 获取token
        jwt_token = request.GET.get("token",None)
        try:
            user_json = jwt_decode_handler(jwt_token)
            # 获取token中的uid
            user_id = user_json['user_id']
        except:
            return Response({"code":405,"message":"用户信息已失效，请重新登录"})
        # 读取私钥及公钥
        app_private_key_string = open("pay/private.txt").read()
        alipay_public_key_string = open("pay/public.txt").read()

        alipay = AliPay(
            appid="支付宝沙箱id",
            app_notify_url= None,  # 默认回调url
            app_private_key_string=app_private_key_string,
            alipay_public_key_string=alipay_public_key_string,
            # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            sign_type="RSA2",  # RSA 或者 RSA2
            debug=True  # 默认False
        )

        # 电脑网站支付，需要跳转到https://openapi.alipay.com/gateway.do? + order_string
        order_str = alipay.api_alipay_trade_page_pay(
            subject="返回名称",
            notify_url=None,
            out_trade_no=order,      # 订单号
            total_amount=price,         # 订单价格
            return_url="http://127.0.0.1:8000/get_alipy/"
        )
	# 拼接支付宝支付页面网址url
        request_url = 'https://openapi.alipaydev.com/gateway.do?' + order_str

        return Response({
            "code": 200,
            "msg": "请求成功，跳转支付页面",
            "data": request_url
        })
```

#### 后端实现（获取回调网站数据）

```html
class Get_Alipy(APIView):
    def get(self,request):
        user = request.query_params
        # 获取支付用户id
        uid = r.get("payuid")
        # 获取订单号及价格  并将单位换算成分
        price = int(float(user["total_amount"])*100)
        order = user["out_trade_no"]
        # 订单表中生成订单
        Order.objects.create(uid=int(uid),order=order,price=price)
        return Response({"code":200,"message":"购买成功"})
```

### 总结

支付宝沙箱环境是一个好东西，不需要商家认证那些，开发者可以直接整代码并且效果和实际上线效果是一样的。是一些技术研究者的福音。

**更多详情请查看**：

- **文档主页**：[https://openhome.alipay.com/developmentDocument.htm](https://openhome.alipay.com/developmentDocument.htm)
- **产品介绍**：[https://docs.open.alipay.com/270](https://docs.open.alipay.com/270)
- **快速接入**：[https://docs.open.alipay.com/270/105899/](https://docs.open.alipay.com/270/105899/)
- SDK：[https://docs.open.alipay.com/270/106291/](https://docs.open.alipay.com/270/106291/)
  - **python对接支付宝SDK**：[https://github.com/fzlee/alipay/blob/master/README.zh-hans.md](https://github.com/fzlee/alipay/blob/master/README.zh-hans.md)
  - **python对接支付宝SDK安装**：`pip install python-alipay-sdk --upgrade`
- **API列表**：[https://docs.open.alipay.com/270/105900/](https://docs.open.alipay.com/270/105900/)
