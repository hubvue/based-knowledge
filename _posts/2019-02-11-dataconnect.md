---
title: 三种数据推送的方案
layout: post
subtitle: 使用comet、socket.io、SSE实现数据推送
date: '2019-02-11 15:01:49'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- socket
---

### 数据推送
我们都知道在前端http连接是无状态的，不能保持连接状态，因此不能保证实时的数据推送。数据推送是一种建立长久连接的一种解决方案。
### 数据推送的方案
#### comet
comet是一个HTTP长连接技术，说白了就是当我们前端与后端建立HTTP链接之后，请求连接不释放，而是不停的往前端发送资源。

comet保持连接的方式又两种：一种是前端隔一段时间发送一次请求、一种是让后端去往前端发送数据。如果使用这样的方案的话使用第二种居多，也就是所谓的服务器推数据。

来使用express实现一下这种方案。

利用express搭一个简单的服务
```javascript
const express = require('express');
const swig = require('swig')
const app = express();
app.set('view engine','html');
app.engine('html',swig.renderFile);
app.get('/data',(req,res) => {
    res.set({
        'Cache-Control' : 'max-age=0',      //让后端输出没有缓存
    })
    while(true){
        const random = Math.random() * 100;
        res.json({
            code : random,
        })
    }

})
app.get('/',(req,res) => {
    res.render('index');
})
app.listen(8080);
```
前端使用fetch调用这个接口。
```javascript
fetch("data").then(res=> res.json()).then(data=> {
    console.log(data);
})
```
#### 基于socket的数据推送方式
socket.io是目前数据推送方案里用的比较火的一个。socket.io 使用socket端口连接，不是使用ajax通信。

下面来使用socket搭建一个简单的聊天室。
##### 使用express搭建一个简单的web应用
```javascript
const express = require('express');
const app = express();

const http = require('http').Server(app);   //引入http模块并注册express应用为服务。

app.get('/',(req,res) =>{
    res.sendFile(__dirname + '/index.html');
})
http.listen(3000,()=>{
    console.log('listen on 3000');
})
```
前端
```html
<!doctype html>
<html>
<head>
    <title>Socket.IO chat</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font: 13px Helvetica, Arial;
        }

        form {
            background: #000;
            padding: 3px;
            position: fixed;
            bottom: 0;
            width: 100%;
        }

        form input {
            border: 0;
            padding: 10px;
            width: 90%;
            margin-right: .5%;
        }

        form button {
            width: 9%;
            background: rgb(130, 224, 255);
            border: none;
            padding: 10px;
        }

        #messages {
            list-style-type: none;
            margin: 0;
            padding: 0;
        }

        #messages li {
            padding: 5px 10px;
        }

        #messages li:nth-child(odd) {
            background: #eee;
        }
    </style>
</head>

<body>
    <ul id="messages"></ul>
    <form action="">
        <input id="m" autocomplete="off" /><button>Send</button>
    </form>
</body>
<!-- 引入socket-->
<script src="/socket.io/socket.io.js"></script>
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<script>
</script>
</html>
```
##### 服务端

###### scoket .io
引入socket.io并监听http模块
```javascript
const io = require('socket.io')(http);
```
监听socket连接事件
```javascript
io.on('connection',(socket) => {
    console.log('建立连接成功')
})
```
监听socket关闭连接事件
```javascript
io.on('disconnect',(socket) => {
    console.log('断开连接');
})
```
监听事件，并将信息推送给所有监听此事件的用户。
```javascript
io.on('connection',(socket) =>{
    console.log('建立连接成功！');
    //监听
    socket.on('chat message',(msg)=>{
        //推送
        io.emit('chat message',msg);
        console.log('message:',msg);
    })
})
```
##### 客户端
初始化socket
```javascript
const socket = io();
```
客户端推送信息
```javascript
$('form').submit(function(e){
    e.preventDefault();
    //向监听此事件的服务端推送信息
    socket.emit('chat message',$("#m").val());
    $('#m').val('');
    return false;
})
```
客户端接受信息。
```javascript
//监听服务端触发事件
socket.on('chat message',(msg)=>{
    $('#messages').append($('<li>').text(msg));
})
```
#### 原生SSE推送
SSE  (Server Send Event)是一种服务器推送的新方式。JavaScript原生API
##### SSE推送的条件
在使用SSE推送的时候，服务端必须设置两个请求头。
1. header('Content-Type:text/event-stream;charset=utf-8')  指定文件类型为事件流
2. header('Access-Control-Allow-Origin:http://127.0.0.1/');    指定哪个域可以访问


##### 服务端
服务端同样也是用一个简单的express应用来实现
```javascript
const express = require('express');
const app = express();

app.get("/",(req,res) =>{
    res.set({
        "Content-Type":"text/event-stream;charset=utf-8",
        "Access-Control-Allow-Origin":"http://127.0.0.1/"
    });
    res.send("时间:"+new Date().getSeconds()+"");
})
app.get("/index",(req,res)=>{
    res.sendFile(__dirname+"/index.html");
})
app.listen(8080);
```
##### 客户端
客户端使用原生JavaScript的EventSource对象来实现数据推送。

初始化EventSource对象
```javascript
source = new EventSource(url)       //指定事件源路径
```
建立连接
```javascript
source.onopen = function(){     //服务器建立连接触发
    console.log('建立连接',this.readyState);
            //this.readyState为连接状态吗。
                        // 0  未开始
                        // 1  连接未完成
                        // 2  连接已完成
}
```
接受数据
```javascript
source.onmessage = function(event){      //用来接受事件响应的数据
    console.log("显示数据：",event.data);
}
```
错误事件
```javascript
socure.onerror = function(err){
    console.log(err);
}
```
###### 完整的代码
```javascript
var socure;
function init(){
    socure = new EventSource("http://127.0.0.1:8080/");
    socure.onopen = function(){
        console.log("建立连接",this.readyState);
    }
    socure.onmessage = function(event){
        console.log("显示数据：",event.data);
    }
    socure.onerror = function(err){
        console.log(err);
    }
}   
init(); 
```