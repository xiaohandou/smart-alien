---
title: "（在线客服系统）Python3.7+Flask1.1.1结合Socket.io与Vue2.9.6联合实现在线客服系统"
tags: ["Flask","Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-06-28 13:54:10"
cover : /img/timg.jpg
---

### 开场

#### websocket是个啥？

webSocket是一种在单个TCP连接上进行全双工通信的协议

webSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输

现在，很多网站为了实现推送技术，所用的技术都是轮询。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。 而比较新的技术去做轮询的效果是Comet。这种技术虽然可以双向通信，但依然需要反复发出请求。而且在Comet中，普遍采用的长链接，也会消耗服务器资源。

在这种情况下，HTML5定义了WebSocket协议，能更好的节省服务器资源和带宽，并且能够更实时地进行通讯

Socket.IO 就是一个封装了 Websocket、基于 Node 的 JavaScript 框架，包含 client 的 JavaScript 和 server 的 Node（现在也支持python,go lang等语言）。其屏蔽了所有底层细节，让顶层调用非常简单，另外，Socket.IO 还有一个非常重要的好处。其不仅支持 WebSocket，还支持许多种轮询机制以及其他实时通信方式，并封装了通用的接口。这些方式包含 Adobe Flash Socket、Ajax 长轮询、Ajax multipart streaming 、持久 Iframe、JSONP 轮询等。换句话说，当 Socket.IO 检测到当前环境不支持 WebSocket 时，能够自动地选择最佳的方式来实现网络的实时通信，这一点就比websocket要智能不少。

### 后端服务搭建

1. 我们需要使用flask框架来实现，首先安装相应的包

   **分别安装Flask本地，跨域模块，以及socketio模块**

   ```python
   pip install flask
   pip install flask-cors
   pip install flask-socketio
   pip install Flask-SQLAlchemy
   ```

   **注意版本**

   ```python
   Flask                   1.1.1
   Flask-Cors              3.0.8
   Flask-SocketIO          4.3.0
   Flask-SQLAlchemy        2.4.1
   ```

2. 接下来，写一个flask的入口文件`manage.py`

   ```python
   from flask import Flask
   from flask_sqlalchemy import SQLAlchemy
   import pymysql
   from flask import request,jsonify
   from flask_cors import CORS
   from flask_socketio import SocketIO,send,emit
   import urllib.parse
   
   pymysql.install_as_MySQLdb()
    
   app = Flask(__name__)
   
   CORS(app,cors_allowed_origins="*")
   
   socketio = SocketIO(app,cors_allowed_origins='*')
    
   @socketio.on('message')
   def handle_message(message):
       message = urllib.parse.unquote(message)
       print(message)
       send(message,broadcast=True)
   
   @socketio.on('connect', namespace='/chat')
   def test_connect():
       emit('my response', {'data': 'Connected'})
   
   @socketio.on('disconnect', namespace='/chat')
   def test_disconnect():
       print('Client disconnected')
    
   if __name__ == '__main__':
       socketio.run(app,debug=True,host="0.0.0.0",port=5000)
   ```

   <u>我们写了三个基于socketio的视图方法，connect和disconnect顾名思义，当clinet发起连接或者断开时我们可以及时捕获到，而message方法就是前后端进行消息通信的重要方法。</u>

   *发送消息的时候方法加了一个broadcast参数，这是socket.io极具特色的功能，类似广播的效果，可以同时给不同链接的client发送消息，即可以用于聊天，也可以用来做消息推送。*

    最后需要注意的一点是，client发送消息时，最好用urlencode编码一下，这样可以解决中文乱码问题，而在server端，可以用urllib.parse.unquote()来进行解码操作。

3. 启动flask服务

   ```python
   python manage.py
   ```

   ![socketio](https://wangxs020202.gitee.io/images/note/wsk1.png)

   **没有出现错误，说明后端没问题**

### Vue搭建页面

1. 指定按本安装依赖

   ```javascript
   npm install vue-socket.io@2.1.0
   ```

2. 在入口文件`main.js`中引入

   ```javascript
   import VueSocketio from 'vue-socket.io';
   
   Vue.use(VueSocketio,'http://127.0.0.1:5000');
   ```

3. 构建用户链接组件`client.vue`

   ```javascript
   <template>
   	<div>
           <div v-for="item in log_list">
               {{item}}
            </div>
           <input v-model="msg" />
           <button @click="send">发送消息</button>
   	</div>
   </template>
   <script>
   export default {
     data () {
       return {
   	  msg: "",
   	  log_list:[]
       }
     },
     //注册组件标签
     components:{
     },
     sockets:{
       connect: function(){
         console.log('socket 连接成功')
       },
       message: function(val){
   	  console.log('返回:'+val);
   	this.log_list.push(val);
   	}
   },
     mounted:function(){
   },
     methods:{
   	send(){
   	  this.$socket.emit('message',encodeURI("用户:"+this.msg));
       },
     }
   }
   </script>
   <style>
   
   </style>
   ```

4. 启动服务，访问页面

   ![socketio](https://wangxs020202.gitee.io/images/note/wsk2.png)

   **没有问题，接下来构建客服组件**

5. 构建客服链接组件`server.vue`

   ```javascript
   <template>
   	<div>
   		<div v-for="item in log_list">
               {{item}}
            </div>
           <input v-model="msg" />
           <button @click="send">发送消息</button>
   	</div>
   </template>
   <script>
   export default {
     data () {
       return {
   	  msg: "",
   	  log_list:[]
       }
     },
     //注册组件标签
     components:{
     },
     sockets:{
       connect: function(){
         console.log('socket 连接成功')
       },
       message: function(val){
   	  console.log('返回:'+val);
   	  this.log_list.push(val);
   	}
   },
     mounted:function(){	
   },
     methods:{
   
   	send(){
   	  this.$socket.emit('message',encodeURI("客服:"+this.msg));
       },  
     }
   }
   </script>
   <style>
   </style>
   ```

   **用来模拟用户和客服分别在不同的电脑进行聊天的场景**

6. 效果展示

   ![socketio](https://wangxs020202.gitee.io/images/note/wsk3.png)

   ![socketio](https://wangxs020202.gitee.io/images/note/wsk4.png)