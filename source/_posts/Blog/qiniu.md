---
title: "（七牛云云端存储）使用python3.7+Vue.js2.0+Django2.0.4异步前端通过api上传文件到七牛云云端存储"
tags: ["Python","Vue","Django"]
categories: ["编程 · 技术"]
date: "2020-06-08"
cover : /img/timg.jpg
---

### 序幕

[云存储](https://baike.baidu.com/item/云存储)的使用范围当下是非常的广泛了。如：某宝，某东这些大型的网上购物平台，许多的商品图片以及视频都开始了云存储。

**那什么是云存储呢**

**云存储**是一种网上[在线存储](https://baike.baidu.com/item/在线存储)（英语：Cloud storage）的模式，即把数据存放在通常由第三方托管的多台虚拟[服务器](https://baike.baidu.com/item/服务器)，而非专属的服务器上。

[云存储](https://baike.baidu.com/item/云存储)是在[云计算](https://baike.baidu.com/item/云计算)(cloud computing)概念上延伸和衍生发展出来的一个新的概念。

比如：七牛云，又拍云....这些云存储服务器

接下来我们来实现七牛云云存储

### 七牛云

#### 配置服务器

1. 首先进入七牛云官网：[https://www.qiniu.com/](https://www.qiniu.com/)，进行`注册登陆`

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu1.png)
   
2. 登陆成功后，进入`管理控制台`，找到`对象存储`，并进入

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu2.png)

3. 进入之后`创建一个空间`

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu3.png)

   **注**：这里值得说一下的是这个`区域存储`，分为5大区域，选择也是根据自己所在的地区选择。下图是不同区域的上传域名

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu4.png)

4. 创建成功之后在`个人头像`处找到`密钥管理`，这里的AK,SK需保存

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu5.png)

   ![qiniu](https://wangxs020202.gitee.io/images/note/qiniu6.png)

#### 安装库

使用七牛云还需要安装一个库

```python
pip install qiniu
```

![qiniu](https://wangxs020202.gitee.io/images/note/qiniu7.png)

#### Django(调用接口获取七牛云token)

```python
# 定义七牛云存储接口
from qiniu import Auth
class QiNiu(APIView):
    def get(self,request):
        # 定义密钥
        q = Auth('AK','SK')
        # 指定上传空间
        token = q.upload_token('创建空间的名称')
        return Response({
            'token': token
        })
```

接口调用返回结果

![qiniu](https://wangxs020202.gitee.io/images/note/qiniu8.png)

#### Vue(获取到token，进行上传文件)

```javascript
<template>
  <div>
    <input @change="upload_qiniu" type="file" >
        上传进度：{{fileload}}
  </div>
</template>
<script>

export default {
  data () {
    return {
      // 七牛云token
      uptoken:'',
      // 进度条图片
      fileload:'',
    }
  },
  //注册组件标签
  components:{

  },
  mounted:function(){
    this.get_uptoken()
},
  methods:{
    // 获取七牛云凭证
    get_uptoken(){
      getqiniu_get().then(res=>{
        this.uptoken = res.token
      })
    },
    // 上传七牛(图片)
    upload_qiniu(e){
      // 获取文件对象
      let file = e.target.files[0];
      // 声明变量
      let param = new FormData();
      // 将上传凭证添加参数
      param.append('token',this.uptoken);
      // 添加文件
      param.append('file',file);
      // 定制化axios
      const axios_qiniu = this.axios.create({withCredentials:false});
      // 发送请求
      axios_qiniu({
        method:'POST',
        url:'http://up-z1.qiniu.com',
        data:param,
        timeout:30000,
        // 上传进度
        onUploadProgress:(e)=>{
          // 计算
          var com = (e.loaded / e.total);
          // 判断
          if(com < 1){
            this.fileload = (com * 100).toFixed(2) + '%'
          }
        },
      }).then(res=>{
        console.log(res)
        // 请求结束
        this.fileload = '100%'
        // this.upload_video()
      })
    },
  }
}
</script>
<style scoped>

</style>
```

页面效果

![qiniu](https://wangxs020202.gitee.io/images/note/qiniu9.png)

上传成功，返回值

![qiniu](https://wangxs020202.gitee.io/images/note/qiniu10.png)

**注**：此处的`key`与`hash`是可以与你七牛云的测试域名拼接访问更上传的文件的

![qiniu](https://wangxs020202.gitee.io/images/note/qiniu11.png)

### 总结

其实七牛云云存储实现起来并不难，主要是通过AK,SK获取上传的token