---
title: "通过高德API和Python3实现通过IP获取地区"
tags: ["Python"]
categories: ["总结 · 笔记"]
date: "2020-07-13 16:59:18"
cover : /img/notepic.jpg
---

### 序幕

[高德地图](https://zh.wikipedia.org/wiki/高德地圖)，是中国领先的[数字地图](https://baike.baidu.com/item/数字地图/10689386)内容、导航和位置服务解决方案提供商。拥有导航[电子地图](https://baike.baidu.com/item/电子地图/1287271)甲级测绘资质、测绘航空摄影甲级资质和互联网地图服务甲级测绘资质“三甲”资质，其优质的电子地图数据库成为公司的核心竞争力。

最新地图浏览器：最新[矢量地图](https://baike.baidu.com/item/矢量地图/5132557)渲染，最高质量地图效果、最丰富数据信息、最快速操作体验、最节省数据流量。专业地图服务：实地采集、网络采集，行业领先。

丰富的出行查询功能：地名信息查询、分类信息查询、公交换乘、驾车路线规划、公交线路查询、位置收藏夹等丰富的基础地理信息查询工具。

成为现代人们生活的必备品

### 获取key并查询

1. 注册成功之后，创建新应用

   进入[控制台](https://lbs.amap.com/dev/)，创建一个新应用。如果您之前已经创建过应用，可直接跳过这个步骤。

   ![de](https://wangxs020202.gitee.io/images/note/gaode1.png)

2. 添加新Key

   在创建的应用上点击"添加新Key"按钮，在弹出的对话框中，依次：输入应用名名称，选择绑定的服务为“web服务API”，如下图所示：

   ![de](https://wangxs020202.gitee.io/images/note/gaode2.png)

   在阅读完高德地图API服务条款后，勾选此选项，点击“提交”，完成 Key 的申请，此时您可以在所创建的应用下面看到刚申请的 Key 了。

   ![de](https://wangxs020202.gitee.io/images/note/gaode3.png)

3. 进入[高德IP定位](https://lbs.amap.com/api/webservice/guide/api/ipconfig/)，并查看官方文档

   ![de](https://wangxs020202.gitee.io/images/note/gaode4.png)

4. 使用已申请的`key`

   第一步，申请”web服务 API”密钥（Key）；

   第二步，拼接HTTP请求URL，第一步申请的Key需作为必填参数一同发送；

   第三步，接收HTTP请求返回的数据（JSON或XML格式），解析数据。

   如无特殊声明，接口的输入参数和输出数据编码全部统一为UTF-8。

5. IP定位API服务地址：

   | URL      | https://restapi.amap.com/v3/ip?parameters |
   | -------- | ----------------------------------------- |
   | 请求方式 | GET                                       |

6. 请求参数

   | 参数名 | 含义             | 规则说明                                                     | 是否必须 | 缺省值 |
   | :----- | :--------------- | :----------------------------------------------------------- | :------- | :----- |
   | key    | 请求服务权限标识 | 用户在高德地图官网[申请Web服务API类型KEY](https://lbs.amap.com/dev/) | 必填     | 无     |
   | ip     | ip地址           | 需要搜索的IP地址（仅支持国内）若用户不填写IP，则取客户http之中的请求来进行定位 | 可选     | 无     |
   | sig    | 签名             | 选择数字签名认证的付费用户必填                               | 可选     | 无     |
   | output | 返回格式         | 可选值：JSON,XML                                             | 可选     | JSON   |

7. 请求接口了解完之后，开始写代码

   ```python
   import requests
   ip = '114.247.50.2'
   url = 'https://restapi.amap.com/v3/ip?ip='+ip+'&output=json&key=66a7ff5f4d2371a783d196becc856f94'
   res = requests.get(url)
   print(res.text)
   ```

8. 调用结果

   ```python
   {"status":"1","info":"OK","infocode":"10000","province":"北京市","city":"北京市","adcode":"110000","rectangle":"116.0119343,39.66127144;116.7829835,40.2164962"}
   ```

9. 这个ip只能我们手动输入，但我们可以使用`socket`模块来获取本机的ip

   ```python
   import socket
   # 获取本机计算机名称
   hostname = socket.gethostname()
   # 获取本机ip
   ip = socket.gethostbyname(hostname)
   print(ip)
   ```

10. 打印结果

    ```python
    192.168.1.1  # 因为我用的以太网，所以获取到的是以太网IP
    ```

11. 两者结合使用

    ```python
    import requests
    import socket
    # 获取本机计算机名称
    hostname = socket.gethostname()
    # 获取本机ip
    ip = socket.gethostbyname(hostname)
    url = 'https://restapi.amap.com/v3/ip?ip='+ip+'.139&output=json&key=66a7ff5f4d2371a783d196becc856f94'
    res = requests.get(url)
    print(res.text)
    ```

### 结语

通过调用高德接口，可以很方便实现通过IP查询地址

**更多内容查看[高德开放平台](https://lbs.amap.com/)**