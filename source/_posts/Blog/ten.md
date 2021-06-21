---
title: "Webshell"
tags: ["Python","Vue"]
categories: ["编程 · 技术"]
date: "2020-05-07"
cover : /img/timg.jpg
---

#### Webshell

“web”的含义是显然需要服务器开放web服务，“shell”的含义是取得对服务器某种程度上操作权限。webshell常常被称为通过网站端口对网站服务器的某种程度上操作的权限。

一方面，webshell被站长常常用于网站管理、[服务器管理](https://baike.baidu.com/item/服务器管理)等等，根据[FSO权限](https://baike.baidu.com/item/FSO权限)的不同，作用有在线编辑网页脚本、上传下载文件、查看数据库、执行任意程序命令等。

另一方面，被入侵者利用，从而达到控制网站服务器的目的。这些[网页脚本](https://baike.baidu.com/item/网页脚本)常称为WEB脚本木马，比较流行的[asp](https://baike.baidu.com/item/asp)或[php](https://baike.baidu.com/item/php)木马，也有基于[.NET](https://baike.baidu.com/item/.NET)的脚本木马与JSP脚本木马。国内常用的WebShell有海阳ASP木马，Phpspy，c99shell等。



`web`端使用`Xterm.js`或者其他的`WebShell`组件和`websocket`

后端只需要支持`WebSocket`和`SSH`协议的远程登录模块即可



#### web端实现

1.安装

`npm install xterm@3.1.0 --save`  指定版本安装，最新版的xterm文件的改动很大，使用下面的方法会报错

2.导包

```javascript
import 'xterm/dist/xterm.css';
import { Terminal } from 'xterm';

// 布局组件 可以不用
import * as fit from 'xterm/lib/addons/fit/fit';
Terminal.applyAddon(fit);
```

3.在页面中的显示

```javascript
<template>
    <div>
        <div id="terminal" style="width: 500px;height:300px;"></div>
    </div>
</template>
```

4.数据操作

```javascript
mounted () {

      //实例化一个websocket，用于和django江湖
      this.terminalSocket = new WebSocket("ws://127.0.0.1:8000/web/");
      //获取到后端传回的信息
      this.terminalSocket.onmessage = (res) => {
          console.log(res.data);
          // var message = JSON.parse(res.data);
          //将传回来的数据显示在xterm里
          this.term.writeln("\r\n"+res.data);
          //重置要发送的信息
          this.order = ""
          //换行，显示下一个开头
          this.term.write("\r\n$ ");
      }
    //ws连接的时候
    // this.terminalSocket.onopen = function(){
    //     console.log('websocket is Connected...')
    // }
    //ws关闭的时候
    // this.terminalSocket.onclose = function(){
    //     console.log('websocket is Closed...')
    // }
    //ws错误的时候
    // this.terminalSocket.onerror = function(){
    //     console.log('damn Websocket is broken!')
    // }
    // this.term.attach(this.terminalSocket)
    // 绑定xterm到ws流中 },
      

      
      let terminalContainer = document.getElementById('terminal')
      //创建xterm实例
      this.term = new Terminal({
        cursorBlink: true, // 显示光标
        cursorStyle: "underline" // 光标样式
        })                     // 创建一个新的Terminal对象
      
      this.term.open(terminalContainer)              // 将term挂载到dom节点上
    },
```

5.web端完整代码

```javascript
<template>
  <div class="console" id="terminal"></div>
</template>
<script>
    import { Terminal } from 'xterm'
    import * as attach from 'xterm/lib/addons/attach/attach'
    import * as fit from 'xterm/lib/addons/fit/fit'
  export default {
    name: 'webssh',
    data () {
      return {
        
        term: null,
        terminalSocket: null,
        order:''
      }
    },
    methods: {
      
      
    },
    mounted () {

      //实例化一个websocket，用于和django江湖
      this.terminalSocket = new WebSocket("ws://127.0.0.1:8000/web/");
      //获取到后端传回的信息
      this.terminalSocket.onmessage = (res) => {
          console.log(res.data);
          // var message = JSON.parse(res.data);
          //将传回来的数据显示在xterm里
          this.term.writeln("\r\n"+res.data);
          //重置要发送的信息
          this.order = ""
          //换行，显示下一个开头
          this.term.write("\r\n$ ");
      }
    //ws连接的时候
    // this.terminalSocket.onopen = function(){
    //     console.log('websocket is Connected...')
    // }
    //ws关闭的时候
    // this.terminalSocket.onclose = function(){
    //     console.log('websocket is Closed...')
    // }
    //ws错误的时候
    // this.terminalSocket.onerror = function(){
    //     console.log('damn Websocket is broken!')
    // }
    // this.term.attach(this.terminalSocket)
    // 绑定xterm到ws流中 },
      

      
      let terminalContainer = document.getElementById('terminal')
      //创建xterm实例
      this.term = new Terminal({
        cursorBlink: true, // 显示光标
        cursorStyle: "underline" // 光标样式
        })                     // 创建一个新的Terminal对象
      
      this.term.open(terminalContainer)              // 将term挂载到dom节点上

      
      //在xterm上显示命令行提示
      this.term.write('$ ')
      //监听xterm的键盘事件
      this.term.on('key', (key, ev)=>{ 
        // key是输入的字符 ev是键盘按键事件
        console.log("key==========", ev.keyCode);
        this.term.write(key) // 将输入的字符打印到黑板中
        if (ev.keyCode == 13) { // 输入回车
            // console.log("输入回车")
            // this.term.write('$ ')
            // console.log(this.order)
            
            //使用webscoket将数据发送到django
            this.terminalSocket.send(this.order)
            // this.order=''
            console.log("里面的order",this.order)
        }else if(ev.keyCode == 8){//删除按钮
          //截取字符串[0,lenth-1]
          this.order = this.order.substr(0,this.order.length-1)

          //清空当前一条的命令
          this.term.write("\x1b[2K\r")
          //简化当前的新的命令显示上
          this.term.write("$ "+this.order)

          console.log("截取的字符串"+this.order)
          typeof this.order
        }else{// 将每次输入的字符拼凑起来
        this.order += key
        console.log("外面的order",this.order)}
        
    })
    },
     
  }
</script>
```



### 服务器端实现

#### 使用paramiko与服务器建立交互

1.安装

`pip install paramiko`

2.利用`paramiko`进行`ssh`远程登录

```python
import paramiko

# 创建SSH客户端
client = paramiko.SSHClient()
# SSH客户端Host策略,目的是接受不在本地Known_host文件下的主机。
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
# SSH客户端开启连接
client.connect(
    hostname="192.168.184.144",port=22,username='xterm',password='123123'
)
# stdout 为正确输出，stderr为错误输出,每次执行完成只有一个变量中有值
stdin, stdout, stderr = client.exec_command("ls")

# 执行ls命令的结果
print(stdout.read().decode('utf-8'))
```

3.结合`websocket`实现实时操作

```python
#wsserver.py
from channels.generic.websocket import WebsocketConsumer
import paramiko
class WebSSHService(WebsocketConsumer):

    def connect(self):
        self.accept()
        self.sh = paramiko.SSHClient()  # 1 创建SSH对象
        self.sh.set_missing_host_key_policy(paramiko.AutoAddPolicy())  # 2 允许连接不在know_hosts文件中的主机
        self.sh.connect("192.168.184.144", username="root", password="beijing2018")  # 3 连接服务器
        print("连接成功")

    def receive(self, text_data=None, bytes_data=None):
        print(str(text_data))  # 打印收到的数据
        print(type(text_data))

        stdin, stdout, stderr = self.sh.exec_command(text_data)

        right_info = stdout.read()
        err_info = stderr.read()
        print(right_info)

        if right_info:
            new_data = right_info.decode("utf-8").replace("\n","\r\n")
            print(new_data)
            self.send(new_data)
        elif err_info:
            new_data = err_info.decode("utf-8").replace("\n", "\r\n")
            print(new_data)
            self.send(new_data)
        else:
            print(self.send("命令执行成功"))


    def disconnect(self, code):
        print(f'sorry{self},你被女朋友抛弃了')
```



