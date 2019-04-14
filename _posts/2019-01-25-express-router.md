---
title: express路由的使用
layout: post
subtitle: express路由的使用方法和解析
date: '2019-01-25 11:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- Express
---

### 什么是路由
路由是指如何定义应用的端点以及如何响应客户端的请求

我们所谓的Controller就是路由控制器

### 路由的组成部分
路由是由url、http请求(GET，post)和若干句柄组成。

路由结构如下：app.METHOD(path,{callback..},callback)
1. app是express对象的一个实例
2. METHOD是一个HTTP请求方法
3. path是服务器上的路径
4. callback是当路由匹配时要执行的函数。

### 路由方法
get、post、put、delete代表CRUD的增删该查
#### get方法
```javascript
app.get('/',(req,res) => {
    res.send('GET 请求')
});
```
#### post方法
```javascript
app.post('/',(req,res) => {
    res.send('POST 请求')
});
```
#### put方法
```javascript
app.put('/',(req,res) => {
    res.send('PUT 请求')
});
```
#### delete方法
```javascript
app.delete('/',(req,res) => {
    res.send('DELETE 请求')
});
```
### 标准的路由格式
标准的路由控制格式为：controller/action  这样的形式，一个控制器对应多个操作ID。

### app.all方法
app.all是一个特殊的路由方法，没有任何HTTP方法与其对象，无论是什么HTTP请求都会命中这个路由方法。
```javascript
app.all((req,res,next) => {
    res.send('hello')       //无论是什么HTTP请求都会命中
    next(); 把当前的这个请求交给下一个请求处理函数
})
```
### 路由路径
 路由路径与请求方法结合，定义可以进行请求的端点。路由路径可以是字符串、字符串模式和正则表达。
 
#### 基于字符串
##### 路径为/
```javascript
app.get('/',(req,res) => {
    res.send('/')       
})
```
##### 路径为/index
```javascript
app.get('/index',(req,res) => {
    res.send('/index');
})
```
##### 路径为/index.html
```javascript
app.get('/index.html',(req,res) => {
    res.send('/index.html');
})
```
#### 基于字符串模式
##### 路径匹配acd和abcd
```javascript
app.get('/ab?cd',(req,res) =>{
    res.send('ab?cd');
})
```
##### 路径匹配 abcd、abbcd、abbbcd等
```javascript
app.get('ab+cd',(req,res) => {
    res.send('ab+cd');
})
```
##### 路径匹配 abcd absdcd abfdafafcd ab1231cd等
```javascript
app.get('/ab*cd',(req,res) => {
    res.send('ab*cd');
})
```
##### 路径匹配 abe abcde
```javascript
app.get('/ab(cd)?e',(req,res)=> {
    res.send('ab(cd)e');
})
```
#### 基于正则表达式的路径匹配
##### 匹配任何包含a的路由
```javascript
app.get('/a/',(req,res) => {
    res.send('/a/');
})
```

### 多个路由处理函数
请求处理多个回调函数，使用next()方法传递请求,如果应用已经响应了用户，后面的响应就不会进行了，node代码可以继续执行。

方式如下：
```javascript
app.get('/index',(req,res,next) => {
    console.log(1);
    next();
},(req,res,next) =>{
    console.log(console.log(req));
})
```
#### 使用回调函数数组处理路由
```javascript
const cb0 = (req,res,next) => {
    console.log('cb0');
    next();
}
const cb1 = (req,res, next) => {
    console.log('cb1');
    next();
}
const cb2 = (req,res,next) => {
    res.send('fdsaf');
}
app.get('/index',[cb0,cb1,cb2]);
```
#### 使用混合函数和函数数组处理路由
```javascript
const cb0 = (req,res,next) => {
    console.log('cb0');
    next();
}
const cb1 = (req,res, next) => {
    console.log('cb1');
    next();
}
app.get('/index/b',[cb0,cb1],(req,res,next) => {
    console.log('hello');
    next();
},(res,req,next) => {
    res.send('wang');
})
```
### 使用app.route()创建路由
app.route方法可以为一个路由路径建立多种请求方法
```javascript
app.route('/index')
    .get((req,res) => {
        console.log('get');
    })
    .post((req,res) => {
        console.log('post');
    })
    .put((req,res)=> {
        console.log('put');
    })
```
### 使用express.Router类创建模块化，可挂在的路由句柄
Router实例是一个完整的中间件和路由系统
#### 使用
```javascript
const express =require('express');
const router = express.Router();
```
#### 使用router定义全局路由(必经)
定义在所有路由的最顶端
```javascript
router.use((req,res,next) => {
    console.log('必经路由');
    next();
})
二者相同
app.use((req,res,next) => {
    console.log('必经路由')
    next();
})
```
 这相当于定义了一个过滤的路由，所以路由句柄都会经过这个路由进行过滤,和*相同。
 
 ### 在应用中加载路由模块
 ```javascript
 const router = require('./router');
app.use('/router',router);
 ```