---
title: express错误处理及log4js日志
layout: post
subtitle: express错误处理中间件的使用及log4js打印日志
date: '2019-01-25 14:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- Express
---

### express的错误处理
错误处理是express的全局一个中间件，错误处理中间件值定义在所有中间件的最下面的。它的工作流程是，只要有错误处理，有错误的中间件就会next到错误处理中间件，否则就不会执行到它。执行到错误处理中间件之后web实例又被重新唤醒继续执行node代码。

### 错误处理中间件
express错误处理中间件有着严格的方法签名：必须要有4个参数，即便是用不到也要写上。
```javascript
app.use((err,req,res,next) => {
    console.log(err);
})
```
1. err是错误对象
2. req是请求对象
3. res是响应对象
4. next是进行下一个中间件的操作

### 两个常用的错误处理中间件

#### 404错误
```javascript
'use strict';
module.exports = function(template){
    return function fileNotFound(req,res,next) {
        var model = {url : req.url,statusCode : 404};
        if(req.xhr){
            res.send(404,model);
        }else {
            res.status(404);
            渲染出错误处理的模板
            res.render(template,req,data);
        }
    }
}
```
#### 500 错误
```javascript
var http = requier('http');
module.exports = function(template){
    return function serverError(err,req,res,next) {
        if(!res.statusCode || res.status != 200){
            res.statusCode = 500;
        }
        var desc = http.STATUS_CORES[res.statusCode];
        res.end(desc + '\n' | err);
    }
}
```

### 日志系统log4js
log4js是第三方日志系统。
#### 安装
> npm install log4js --save

#### 使用
```JavaScript
const log4js = require('log4js');
log4js.configure({
    appenders : {
        log : {     //日志名字   可以写多个
            type : 'file',      //日志类型
            filename : './logs/log.log'     //日志存放路径
        }
    },
    categories : {
        default : {
            appenders : ['log']     //日志名字放在这
            level : 'info'      //日志等级，只会打印相同等级或者比此等级低的日志
        }
    }
})
var logger = log4js.getLogger('log');   //获取到此日志
app.use(log4js.connectLogger(logger));  //载入中间件
```
#### 日志等级区分
> ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < MARK < OFF

#### 输出结果
```log
[2019-01-25T13:45:58.890] [INFO] log - ::ffff:127.0.0.1 - - "GET /err HTTP/1.1" 404 142 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
[2019-01-25T13:46:02.370] [INFO] log - ::ffff:127.0.0.1 - - "GET / HTTP/1.1" 304 - "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
[2019-01-25T13:47:16.910] [INFO] log - ::ffff:127.0.0.1 - - "GET /test HTTP/1.1" 500 1582 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
[2019-01-25T13:48:39.321] [INFO] log - ::ffff:127.0.0.1 - - "GET /test HTTP/1.1" 500 1582 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
[2019-01-25T13:48:40.037] [INFO] log - ::ffff:127.0.0.1 - - "GET /test HTTP/1.1" 500 1582 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
[2019-01-25T13:48:40.220] [INFO] log - ::ffff:127.0.0.1 - - "GET /test HTTP/1.1" 500 1582 "" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"
```
#### 封装日志中间件
```javascript
const log4js = require('log4js');
module.exports = (type='file',path='./log.log',level='info')=> {
    log4js.configure({
        appenders : {
            log : {     //日志名字   可以写多个
                type : type,      //日志类型
                filename : path     //日志存放路径
            }
        },
        categories : {
            default : {
                appenders : ['log']     //日志名字放在这
                level : level     //日志等级，只会打印相同等级或者比此等级低的日志
            }
        }
    })
var logger = log4js.getLogger('log');   //获取到此日志
    return log4js.connectLogger(logger);
}
```