---
title: express中间件
layout: post
subtitle: express中间件的使用及开发一个简单的中间件
date: '2019-01-25 12:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- Express
---

### 所谓中间件
 从请求的一端到响应的一端中间所使用到的插件 叫做中间件。其中控制请求和响应循环流程的叫做next。next表示是否要进入下一个中间件处理。
 
 > 注：在这里必须要明白的一点就是，express是基于中间件的，所有的句柄函数都是中间件。
 
 
### 中间件的功能
 1. 执行任何代码
 2. 修改请求和响应对象    
 3. 终结请求-响应循环。
 4. 调用堆栈的下一个中间件
 如果当前中间件没有终结请求响应循环(res.render,res.send,res.json等)，则必须调用next()方法把控制权交给下一个中间件。如果终止了的话，下一个中间件渲染会报错，但是node代码正常执行。

### Express应用中间件的类型
1. 应用级中间件
2. 路由级中间件
3. 错误处理中间件
4. 内置中间件
5. 第三方中间件

### 应用级中间件
应用级中间件绑定到app对象使用app.use()和app.METHOD方法(HTTP方法)
```javascript
const app = express();
    //没有挂在路径的中间件，应用的所有请求都会执行该中间件
    app.use((req,res,next) => {
        console.log('必经路由')
        next();
    })
    //挂在路径的中间件，任何指向此路径的中间件都会执行它
    app.use('/user/:id',(req,res.next) => {
        console.log('/user/中间件')
        next();
    })
    //使用http方法的中间件
    app.get('/user/:id',(req,res,next) => {
        res.send('http方法中间件')
    })
```
#### 装载组中间件
装载组中间件就是对一个挂载点挂载一组中间件。
```javascript
app.use(/user/:id,(req,res,next) => {
    console.log('one');
    next();
},(req,res,next) => {
    console.log('two');
    next();
})
```
如果在中间件栈中跳过剩余的中间件，可以使用next('route')方法将控制权交给下一个路由。

注意：next('route')只对使用app声明的和router声明的中间件有效。

### 路由级中间件
路由级中间件和应用级中间件一样，只是它绑定的对象为express.Router();
```javascript
const router = express.Router()
```
router中间件和app中间件处理的情况一样，只是router中没有app中特别复杂的api，只有路由。参照应用级中间件的使用方法。

### 错误处理中间件
错误处理中间件有4个参数，定义错误处理中间件时必须使用这4个参数，即使不需要next对象，也必须在方法签名中声明它，否则把会错误处理中间件当做常规中间件处理。错误中间件容错要放在所有中间件后面。
```javascript
app.use((err,req,res,next) => {
    console.log(err.stack);
    res.status(500).send('服务端出错');
})
```
### 内置中间件
 express.static是Express唯一内置的中间件，它基于server-static,负责在Express中托管静态资源.
 ```javascript
 app.use(express.static('public'));
 ```
 
### 第三方中间件
安装第三方中间件，通过使用app.use的方式把第三方中间件加载到应用里面，可以在应用级加载，也可以在路由级加载。

比如cookie-parse：用于解析cookie,如果不使用这个，就拿不到cookie。
#### 安装cookie-parser
> npm install cookie-parser

```javascript
const cookieParser = require('cookie-parser');
app.use(cookieParser());    同样这个也是一个应用级中间件，cookieParser也是一个方法

app.use(function cookieParser(req,res,next){
    //处理cookie并注册到cookies上
    req.cookie = function(){

    }
    next();
})
```

### 如何开发一个第三方中间件
由上面总结可得知，一个中间件就是一个函数，而第三方中间件同样也是一个全局的应用级中间件。通过这些信息就可以写出一个中间件。

例如：开发一个事件中间件，在req上绑定使用事件
```javascript
const timeMiddleware() => (req,res,next) => {
    req.requestTime = Date.now();
    //更复杂的处理逻辑
    next();
}
module.exports = timeMiddleware;
```
#### 引入和使用
```javascript
const timeMiddle = require('timeMiddleware');
app.use(timeMiddle());
app.get('/',(req,res) => {
    console.log(req.requestTime);
})
```
这样一个简单的中间件就完成了。