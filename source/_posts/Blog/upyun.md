---
title: "（又拍云云存储）使用python3.7+Vue.js2.0+Django2.0.4实现又拍云云存储的异步文件上传功能"
tags: ["Python","Vue","Django"]
categories: ["编程 · 技术"]
date: "2020-06-09 15:54"
cover : /img/timg.jpg
---

### 序幕

上次我们实现了[七牛云异步上次文件](https://www.sirxs.cn/2020/06/08/Blog/qiniu/)，今天我们来实现又拍云的异步文件上传功能

话不多说，直接实现吧

### 又拍云

#### 配置服务器

1. 首先进入又拍云官网：[https://www.upyun.com/](https://www.upyun.com/)，进行`注册登陆`

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun1.png)

2. 登陆成功后，进入`控制台`,找到`云服务`，并进入

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun2.png)

3. 进入之后，`创建服务`

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun3.png)

4. 这里与七牛云不同的是需要我们`新建一个授权操作员`

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun4.png)

5. 进入`创建操作员`之后，将权限全部勾选，而且这个密码是一次性密码，需要我们记录

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun5.png)

6. 创建服务成功之后，进入`存储管理`，下面可以定义操作员`权限`

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun6.png)

   ![upyun](https://wangxs020202.gitee.io/images/note/upyun7.png)

#### 安装库

使用又拍云还需要安装一个库

```python
pip install upyun
```

![upyun](https://wangxs020202.gitee.io/images/note/upyun8.png)

### Django(获取文件，并上传)

```python
import upyun

#定义文件上传类
class UploadFile(View):
    def post(self,request):
        img = request.FILES.get('file')
        up = upyun.UpYun('你的空间名称', username='操作员账号', password='操作员密码')
        headers = { 'x-gmkerl-rotate': '180' }
        for chunk in img.chunks():
            res = up.put('/touxiang1.jpg', chunk, checksum=True, headers=headers)
        #返回结果
        return HttpResponse(json.dumps({'filename':img.name}),content_type='application/json')
-----------------------------------------------------------------------------------------------------
#多线程上传视频文件
import upyun
import threading
class UpYunVideo(APIView):
	def post(self,request):
		# 获取视频文件
		video = request.FILES.get('file')
		print(video)
		name = video.name
		up = upyun.UpYun('你的空间名称','操作员账号','操作员密码')
		# 分块上传
		# 创建一个分块上传实例(设置上传路径)
		uploader = up.init_multi_uploader('/edu/%s'%name)
		threads = []
		t1 = threading.Thread(target=uploader.upload,args=(0,'分割文件'))
		threads.append(t1)
		t2 = threading.Thread(target=uploader.upload,args=(1,'分割文件'))
		threads.append(t2)
		# 启动多线程
		for t in threads:
			t.start()
			# 阻塞主进程
			t.join()
		# 调用结束
		res = uploader.complete()
		return HttpResponse(json.dumps({'filename':name}),content_type='application/json')
```



### Vue(获取视频数据，传给后端)

```javascript
<template>
  <div>
      <input @change="upyun_video" type="file" >
      上传进度：{{fileloadv}}
  </div>
</template>
<script>
export default {
  data () {
    return {
      // 进度条视频
      fileloadv:'',
    }
  },
  //注册组件标签
  components:{
  },
  mounted:function(){
},
  methods:{
    // 上传七牛(视频)
    upyun_video(e){
      // 获取文件对象
      let file = e.target.files[0];
      // 声明变量
      let param = new FormData();
      // 添加文件
      param.append('file',file);
      const config = {
        headers :{
          'Content-Type': 'multipart/form-data'
        }
      }
      // 定制化axios
      const axios_upyun = this.axios.create({withCredentials:false});
      axios_upyun({
        method:'POST',
        url:'http://localhost:8000/upyun/',
        data:param,
        timeout:30000,
        config:config,
        // 上传进度
        onUploadProgress:(e)=>{
          // 计算
          var com = (e.loaded / e.total);
          // 判断
          if(com < 1){
            this.fileloadv = (com * 100).toFixed(2) + '%'
          }
        },
      }).then(res=>{
         console.log(res)
         this.fileloadv = '100%'
      })
    },
  }
}
</script>
<style scoped>

</style>
```

页面效果

![upyun](https://wangxs020202.gitee.io/images/note/upyun9.png)

上传成功，返回值

![upyun](https://wangxs020202.gitee.io/images/note/upyun10.png)

查看文件(上传成功)

![upyun](https://wangxs020202.gitee.io/images/note/upyun11.png)

同时，又拍云也可以用测试域名（CNAME）+ 设置的路径 + 获取到的名称，来实现查看文件

![upyun](https://wangxs020202.gitee.io/images/note/upyun12.png)

### 总结

又拍云上传文件与七牛云上传文件其实，实现的过程没有什么太大的区别，之重要的是又拍云的免费流量比七牛云多出大概5g左右，当然了得加入一个所谓的又拍云联盟：[https://www.upyun.com/league](https://www.upyun.com/league)