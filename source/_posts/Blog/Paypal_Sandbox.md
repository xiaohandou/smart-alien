---
title: "（贝宝模拟实现跨境支付）使用python3.7+Vue.js2.0+Django2.0.4实现Paypal模拟跨境支付功能"
tags: ["Django","Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-06-17 15:54"
cover : /img/timg.jpg
---

### 序幕

1. [Paypal(贝宝)](https://baike.baidu.com/item/PayPal)，作为一种外贸支付方式，目前在国际贸易支付服务中倍受亿万用户追捧，是全球商户和消费者最受欢迎的电子支付方式之一，是倍受全球亿万用户追捧的国际贸易[支付工具](https://baike.baidu.com/item/支付工具)，即时支付，即时到账，全中文操作界面，能通过中国的本地银行轻松提现，解决外贸收款难题，助您成功开展海外业务，决胜全球。注册PayPal后就可立即开始接受信用卡付款。、
2. PayPal是名副其实的全球化支付平台，  服务范围超过200个市场， 支持的币种超过100个。在跨国交易中， 将近70%的在线跨境买家更喜欢用PayPal支付海外购物款项。
3. 之前写过模拟实现[支付宝模拟支付](https://www.sirxs.cn/2020/06/13/Blog/Alipay_Sandbox/)，这次我们来实现跨境三方支付接口PayPal

### 创建贝宝沙盒应用

1.  首先注册官网 [https://www.paypal.com](https://www.paypal.com) 以及开发者平台：[https://developer.paypal.com/classic-home/](https://developer.paypal.com/classic-home/)

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal1.png)

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal2.png)

2. 注册成功后，在沙盒的账号控制页面：[https://developer.paypal.com/developer/accounts/](https://developer.paypal.com/developer/accounts/)

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal3.png)

   **与支付宝沙箱一样，也有两个账号，一个商家，一个个人，当然也可以自己创建账号，点击蓝色按钮，即可创建**

3. 接下来，我们要修改一下个人用户的信息

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal4.png)

   **Email ID 是支付的时候登陆的账号，密码建议修改**

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal5.png)

   **接下来，我们修改一下`Funding`中的余额，以便我们测试使用**

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal6.png)

4. 之后，进入应用管理页面：[https://developer.paypal.com/developer/applications/](https://developer.paypal.com/developer/applications/)

   **发现已有一个创建好的支付应用，并进入**

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal11.png)

5. 记录下它的client_id和client_secret，会用到

   ![paypal](https://wangxs020202.gitee.io/images/note/paypal12.png)

6. 做完这些之后，我们对沙箱的操作就已经完成了，我们进入下一步

### 安装PayPal的SDK，并进行测试

#### 安装SDK

直接在终端窗口输入：

```python
pip3 install paypalrestsdk
```

完成下载:

![paypal](https://wangxs020202.gitee.io/images/note/paypal7.png)

#### 构建视图

```python
import paypalrestsdk
def payment(request):
    paypalrestsdk.configure({
      "mode": "sandbox", # sandbox代表沙盒
      "client_id": "你的client_id,
      "client_secret": "你的client_secret" 
    })

    payment = paypalrestsdk.Payment({
        "intent": "sale",
        "payer": {
            "payment_method": "paypal"},
        "redirect_urls": {
            "return_url": "http://localhost:8000/palpay/pay/",#支付成功跳转页面
            "cancel_url": "http://localhost:3000/paypal/cancel/"},#取消支付页面
        "transactions": [{
            "amount": {
                #价格，精确到分
                "total": "5.00",
                #货币种类
                "currency": "USD"},
            "description": "这是一个订单测试"}]})

    if payment.create():
        print("Payment created successfully")
        for link in payment.links:
            if link.rel == "approval_url":
                approval_url = str(link.href)
                print("Redirect for approval: %s" % (approval_url))
                #直接重定向到支付页面
                return redirect(approval_url)
    else:
        print(payment.error)
        return HttpResponse("支付失败")
```

**return_url是支付成功后回调的页面，paypal会将一个支付者id回传，然后服务端需要验证支付才能真的完成支付**

**在支付之前会提示输入`账号`与`密码`是沙箱环境的账号与密码**

![paypal](https://wangxs020202.gitee.io/images/note/paypal9.png)

登陆成功之后跳转支付页面

![paypal](https://wangxs020202.gitee.io/images/note/paypal8.png)

支付完成之后，会跳转到我们定义的回调页面，并返回给我们参数：

`http://localhost:8000/palpay/pay/?paymentId=PAYID-L3SYORA3C031930S1733650J&token=EC-9TG269735K620131N&PayerID=ETYYRCDN8C3XJ  `

这里paypal会传过来三个参数，支付id,token和支付者id

此时，我们的余额还没有扣除，需要用支付者的id进行确认

```python
def payment_execute(request):
    paymentid = request.Get.get("paymentId") #订单id
    payerid = request.Get.get("PayerID")  #支付者id
    payment = paypalrestsdk.Payment.find(paymentid)
    if payment.execute({"payer_id": payerid}):
        print("Payment execute successfully")
        return HttpResponse("支付成功")
    else:
        print(payment.error) # Error Hash
        return HttpResponse("支付失败")
```

**确定成功之后，paypal会扣除余额**

![paypal](https://wangxs020202.gitee.io/images/note/paypal10.png)

自此，一次交易完成

#### 根据订单号，退款

通过获取到的`paymentId`，查询到该订单的交易明细

```python
#明细
payment = paypalrestsdk.Payment.find("paymentId")
print(payment)
```

![paypal](https://wangxs020202.gitee.io/images/note/paypal13.png)

可以看到通过`paymentId`获取到了交易的状态，流水id，以及创建日期。

**通过获取到的流水号进行退款业务**

```python
#退款
from paypalrestsdk import Sale
sale = Sale.find("流水号")
# Make Refund API call
# Set amount only if the refund is partial
refund = sale.refund({
    "amount": {
        "total": "5.00",
        "currency": "USD"}})
# Check refund status
if refund.success():
    print("Refund[%s] Success" % (refund.id))
else:
    print("Unable to Refund")
    print(refund.error)
```

### 总结

总体而言，没有什么特别的难度，整个支付流程相对支付宝来说，更加的紧凑，但是做支付安全是第一要务，就个人体验（仅是个人体验）层面来说，支付宝在安全方面做的还是要比Paypal略强一些，起码在信用卡欺诈和盗刷方面风控做的更好，在风险保障和赔付方面都有提供保险，当然由于金融环境的差异较大，并不是说Paypal的风控做的不好，只是机制不同，在国外，如果持卡人的信用卡被盗刷，一般发卡组织会让商家去承担责任，而国内只能在交易环节设置更多的验证，本质上说是要持卡人承担责任。这也是为什么支付宝的风控看起来更好的原因。

  最后就是关于费率问题，Paypal官方给出的费率是每笔交易收取3.9%+$0.3（根据你的交易流水，比例可以优惠，具体下限看接入者的月营业额度），不过这可是美刀，不得不说这个费率是相当的高，但是国内做境外支付的电商，一般还是要接入Paypal作为支付方式。支付宝的费率基本上在1.2%左右，具体的费率也看交易流水，有实力的下限可以做到基本没有，单纯的看费率似乎支付宝更有优势，但是别忘记了，这样对比是不科学的，因为凡是接入Paypal的都是看中覆盖外币业务的地区，费率则是投资人该考虑的问题了