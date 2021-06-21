---
title: "Django2.0.4与Vue联合实现Gitee(码云)三方登陆，以及什么是OAuth2.0认证和授权机制基本流程"
tags: ["Django","Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-06-02 20:46:18"
cover : /img/timg.jpg
---

### 序幕

[码云(Gitee)](https://baike.baidu.com/item/开源中国),一款强大的代码托管、质量检测、代码演示、团队协作等开发工具集成到云平台，为了构建更好的码云生态环境，推出了基于OAuth2的API V5版本。

### 码云三方登陆

#### 应用创建

1. 首先，我们注册[码云](https://gitee.com/)

2. 创建完成之后点击`个人头像`，找到`设置`

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee1.png)

   

3. 在`设置`里找到`第三方应用`

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee2.jpg)

   

4. 点击`创建应用`

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee3.png)

   

5. `应用名称`，`应用描述`，`应用主页`，`回调地址`根据自己定义修改

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee6.png)

   

6. 创建完毕之后，会给你`APPID`与`APPkey`，这两样要记住

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee7.png)

   

7. 之后的路程可以参照[Gitee开发文档](https://gitee.com/api/v5/oauth_doc#/)

#### 代码实现

1. Vue

   ```vue
   函数名(){
           var appid = 'APPID'
           var redirect_uri = '码云回调地址'
           // 构造url
           var url = 'https://gitee.com/oauth/authorize?client_id='+appid+'&redirect_uri='+redirect_uri+'&response_type=code'
           // 进行跳转
           window.location.href = url;
         },
   ```

   

2. Django

   ```python
   class MayunBack(View):
       def get(self,request):
           # 获取code参数
           code = request.GET.get("code",None)
           # 发起调用，获取access_token
           res = requests.post("https://gitee.com/oauth/token?grant_type=authorization_code&code=%s&client_id=APPID&redirect_uri=http://localhost:8000/gitee_back&client_secret=APPKEY" % code)
           print(res.text)
           return HttpResponse(res.text)
   ```

3. 在获取到`access_token`之后，我们来获取Gitee的授权用户名，进入[Gitee开发文档](https://gitee.com/api/v5/oauth_doc#/)，点击`API文档`

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee4.png)

   

4. 找到`获取授权用户的资料`并打开，点击右上角`申请授权`，就可以获取到授权用户的用户名了

   

   ![gitee](https://wangxs020202.gitee.io/images/note/gitee5.png)

   

5. 代码获取(将上面获取到的access_token传入url)

   ```python
   res_token = requests.get("https://gitee.com/api/v5/user?access_token="+access_token)
       res_token_dict = json.loads(res_token.text)
       name = res_token_dict['name']
   ```

### OAuth2.0认证和授权机制基本流程

**OAuth2.0**

第三方登录是应用开发中的常用功能，通过第三方登录，我们可以更加容易的吸引用户来到我们的应用中。现在，很多网站都提供了第三方登录的功能，在他们的官网中，都提供了如何接入第三方登录的文档。但是，不同的网站文档差别极大，各种第三方文档也是千奇百怪，同时，很多网站提供的SDK用法也是各不相同。对于不了解第三方登录的新手来说，实现一个支持多网站第三方登录的功能可以说是极其痛苦。

实际上，大多数网站提供的第三方登录都遵循OAuth协议，虽然大多数网站的细节处理都是不一致的，甚至会基于OAuth协议进行扩展，但大体上其流程是一定的。今天，我们就来看看基于OAuth2的第三方登陆功能是这样一个流程。

**OAuth2.0的基本流程**

OAuth协议目前已经升级到了2.0，大部分的网站也是支持OAuth2.0的，因此让我们先看看OAuth2。

![oau](https://pic2.zhimg.com/80/5e27c9064ca22c7e54a1d395d679f8b5_720w.jpg)

上图中所涉及到的对象分别为：

- Client 第三方应用，我们的应用就是一个Client
- Resource Owner 资源所有者，即用户
- Authorization Server 授权服务器，即提供第三方登录服务的服务器，如Github
- Resource Server 拥有资源信息的服务器，通常和授权服务器属于同一应用

根据上图的信息，我们可以知道OAuth2的基本流程为：

1. 第三方应用请求用户授权。
2. 用户同意授权，并返回一个凭证（code）
3. 第三方应用通过第二步的凭证（code）向授权服务器请求授权
4. 授权服务器验证凭证（code）通过后，同意授权，并返回一个资源访问的凭证（Access Token）。
5. 第三方应用通过第四步的凭证（Access Token）向资源服务器请求相关资源。
6. 资源服务器验证凭证（Access Token）通过后，将第三方应用请求的资源返回。

### 总结

总之，码云登陆接入相对于新浪而言简单了许多