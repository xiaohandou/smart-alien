---
title: "Websocket即时通讯"
tags: ["Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-05-06"
cover : /img/timg.jpg
---

### `Websocket` 即时通讯

### 1.需求

即时通讯工具一定要保障的是**即时性**

基于现在的通讯协议`HTTP`要如何保障即时性呢?

### 2.短连接型

基于`HTTP`短连接如何保障数据的**即时性**

`HTTP`的特性就是无状态的短连接,即一次请求一次响应断开连接失忆,这样服务端就无法主动的去寻找客户端给客户端主动推送消息

1.轮询

即:客户端不断向服务器发起请求索取消息

优点:基本保障消息即时性

缺点:大量的请求导致客户端和服务端的压力倍增

2.长轮询

即:客户端向服务器发起请求,在`HTTP`最大超时时间内不断开请求获取消息,超时后重新发起请求

优点:基本保障消息即时性

缺点:长期占用客户端独立线程,长期占用服务端独立线程,服务器压力倍增

### 3.长连接型

基于`socket`长连接,由于长连接是双向且有状态的保持连接,所以服务端可以有效的主动的向客户端推送数据

1.`socketio`长连接协议

优点:消息即时,兼容性强

缺点:接入复杂度高,为保障兼容性冗余依赖过多较重

2.`websocket`长连接协议

优点:消息即时,轻量级,灵活适应多场景,机制更加成熟

缺点:相比`socket`兼容性较差

总体来说,`Socketio`紧紧只是为了解决通讯而存在的,而`Websocket`是为了解决更多更复杂的场景通讯而存在的

这里推荐`Websocket`的原因是因为,我们的`Django`框架甚至是`Flask`框架,都有成熟的第三方库

而且`Tornado`框架集成`Websocket`

### 4.`Django`实现`Websocket`

使用`Django`来实现`Websocket`服务的方法很多在这里我们推荐技术最新的`Channels`库来实现

### 4.1.安装`DjangoChannels`

`Channels`安装*如果你是`Windows`操作系统的话,那么必要条件就是`Python3.7`*

```
pip install channels
```

### 4.2.配置`DjangoChannels`

1.创建项目`ChannelsReady`

```
django-admin startprobject ChannelsReady
```

2.在项目的`settings.py`同级目录中,新建文件`routing.py`

```python
# routing.py
from channels.routing import ProtocolTypeRouter

application = ProtocolTypeRouter({
    # 暂时为空
})
```

3.在项目配置文件`settings.py`中写入

```python
INSTALLED_APPS = [
    'channels'
]

ASGI_APPLICATION = "ChannelsReady.routing.application"
```

### 4.3.启动带有`Channels`提供的`ASGI`的`Django`项目

```
You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
February 01, 2020 - 17:27:13
Django version 3.0.2, using settings 'ChannelsReady.settings'
Starting ASGI/Channels version 2.4.0 development server at http://0.0.0.0:8000/
Quit the server with CTRL-BREAK.
```

很明显可以看到`ASGI/Channels`,这样就算启动完成了

### 4.4.创建`Websocket`服务

1.创建一个新的应用`chats`

```python
python manage.py startapp chats
```

2.在`settings.py`中注册`chats`

```python
INSTALLED_APPS = [
    'chats',
    'channels'
]
```

3.在`chats`应用中新建文件`chatService.py`

```python
from channels.generic.websocket import WebsocketConsumer
# 这里除了 WebsocketConsumer 之外还有
# JsonWebsocketConsumer
# AsyncWebsocketConsumer
# AsyncJsonWebsocketConsumer
# WebsocketConsumer 与 JsonWebsocketConsumer 就是多了一个可以自动处理JSON的方法
# AsyncWebsocketConsumer 与 AsyncJsonWebsocketConsumer 也是多了一个JSON的方法
# AsyncWebsocketConsumer 与 WebsocketConsumer 才是重点
# 看名称似乎理解并不难 Async 无非就是异步带有 async / await
# 是的理解并没有错,但对与我们来说他们唯一不一样的地方,可能就是名字的长短了,用法是一模一样的
# 最夸张的是,基类是同一个,而且这个基类的方法也是Async异步的

class ChatService(WebsocketConsumer):
    # 当Websocket创建连接时
    def connect(self):
        pass
    
    # 当Websocket接收到消息时
    def receive(self, text_data=None, bytes_data=None):
        pass
    
    # 当Websocket发生断开连接时
    def disconnect(self, code):
        pass
```

### 4.5.为`Websocket`处理对象增加路由

1.在`chats`应用中,新建`urls.py`

```python
from django.urls import path
from chats.chatService import ChatService
websocket_url = [
    path("ws/",ChatService)
]
```

2.回到项目`routing.py`文件中增加`ASGI`非`HTTP`请求处理

```python
from channels.routing import ProtocolTypeRouter,URLRouter
from chats.urls import websocket_url

application = ProtocolTypeRouter({
    "websocket":URLRouter(
        websocket_url
    )
})
```

#### 总结：

1. 下载
2.  注册到setting.py里的app
3. 在setting.py同级的目录下注册channels使用的路由----->routing.py
4. 将routing.py注册到setting.py
5. 把urls.py的路由注册到routing.py里
6. 编写wsserver.py来处理websocket请求

### 5.websocket客户端

#### 5.1.基于vue的websocket客户端

```vue
<template>
    <div>
        <input type="text" v-model="message">
        <p><input type="button" @click="send" value="发送"></p>
        <p><input type="button" @click="close_socket" value="关闭"></p>
    </div>
</template>


<script>
export default {
    name:'websocket1',
    data() {
        return {
            message:'',
            testsocket:''
        }
    },
    methods:{
        send(){
           
        //    send  发送信息
        //    close 关闭连接

            this.testsocket.send(this.message)
            this.testsocket.onmessage = (res) => {
                console.log("WS的返回结果",res.data);         
            }

        },
        close_socket(){
            this.testsocket.close()
        }

    },
    mounted(){
        this.testsocket = new WebSocket("ws://127.0.0.1:8000/ws/") 


        // onopen     定义打开时的函数
        // onclose    定义关闭时的函数
        // onmessage  定义接收数据时候的函数
        // this.testsocket.onopen = function(){
        //     console.log("开始连接socket")
        // },
        // this.testsocket.onclose = function(){
        //     console.log("socket连接已经关闭")
        // }
    }
}
</script>
```

### 6.广播消息

#### 6.1客户端保持不变，同时打开多个客户端

#### 6.2服务端存储每个链接的对象

```python
socket_list = []

class ChatService(WebsocketConsumer):
    # 当Websocket创建连接时
    def connect(self):
        self.accept()
        socket_list.append(self)


    # 当Websocket接收到消息时
    def receive(self, text_data=None, bytes_data=None):
        print(text_data)  # 打印收到的数据
        for ws in socket_list:  # 遍历所有的WebsocketConsumer对象
        ws.send(text_data)  # 对每一个WebsocketConsumer对象发送数据

```

### 7.点对点消息

#### 7.1客户端将用户名拼接到url，并在发送的消息里指明要发送的对象

```vue
<template>
    <div>
        <input type="text" v-model="message">
        <input type="text" v-model="user">

        <p><input type="button" @click="send" value="发送"></p>
        <p><input type="button" @click="close_socket" value="关闭"></p>
    </div>
</template>


<script>
export default {
    name:'websocket1',
    data() {
        return {
            message:'',
            testsocket:'',
            user:''
        }
    },
    methods:{
        send(){
           
        //    send  发送信息
        //    close 关闭连接
            var data1 = {"message":this.message,"to_user":this.user}
           
            this.testsocket.send(JSON.stringify(data1))
            this.testsocket.onmessage = (res) => {
                console.log("WS的返回结果",res.data);         
            }

        },
        close_socket(){
            this.testsocket.close()
        },
        generate_uuid: function() {
            var d = new Date().getTime();
            if (window.performance && typeof window.performance.now === "function") {
                d += performance.now(); //use high-precision timer if available
            }
            var uuid = "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
                /[xy]/g,
                function(c) {
                var r = (d + Math.random() * 16) % 16 | 0;
                d = Math.floor(d / 16);
                return (c == "x" ? r : (r & 0x3) | 0x8).toString(16);
                }
            );
            return uuid;
        },

    },
    mounted(){
        var username = this.generate_uuid();
        console.log(username)
        this.testsocket = new WebSocket("ws://127.0.0.1:8000/ws/"+ username +"/") 
        console.log(this.testsocket)

      	this.testsocket.onmessage = (res) => {
                console.log("WS的返回结果",res.data);         
            }
      	
        // onopen     定义打开时的函数
        // onclose    定义关闭时的函数
        // onmessage  定义接收数据时候的函数
        // this.testsocket.onopen = function(){
        //     console.log("开始连接socket")
        // },
        // this.testsocket.onclose = function(){
        //     console.log("socket连接已经关闭")
        // }
    }
}
</script>
```



#### 7.2服务端存储用户名以及websocketConsumer，然后给对应的用户发送信息

```python
from channels.generic.websocket import WebsocketConsumer
user_dict ={}
list = []
import json
class ChatService(WebsocketConsumer):
    # 当Websocket创建连接时
    def connect(self):
        self.accept()
        username = self.scope.get("url_route").get("kwargs").get("username")
        user_dict[username] =self
        print(user_dict)

        # list.append(self)


    # 当Websocket接收到消息时
    def receive(self, text_data=None, bytes_data=None):
        data = json.loads(text_data)
        print(data)
        to_user = data.get("to_user")
        message = data.get("message")

        ws = user_dict.get(to_user)
        print(to_user)
        print(message)
        print(ws)
        ws.send(text_data)


    # 当Websocket发生断开连接时
    def disconnect(self, code):
        pass




```

