---
title: express模板引擎的使用
layout: post
subtitle: experss模板引擎的使用
date: '2019-01-25 14:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- Express
---

### 什么是模板引擎
模板引擎就是使我们能够在应用程序中使用静态模板文件。在运行时，模板引擎用实际值替换模板文件中的变量，并将模板转换为发送到客户端的HTML文件。通俗的讲就是使用一个模板可以在里面写变量，写循环，然后通过服务端编译程html文件。

### swig.js 模板引擎
swig.js是一个html模板引擎，它支持.html后缀名。
#### 安装swig
> npm install swig --save

#### 引入swig
```javascript
const swig = require('swig');
```
#### 设置模板引擎为.html
```JavaScript
app.set('view engine','html');
```
#### 设置让swig来编译模板引擎
```javascript
app.engine('html',swig.renderFile);
```

### 基础模板
在项目根目录创建layout文件夹存放基础模板，在其中创建基础的模板文件layout.html,基础就是通用的模板，可以让模板继承自基础模板，省去重复的代码。内容如下。

![](https://user-gold-cdn.xitu.io/2019/1/25/16883c0286ae75dc?w=826&h=340&f=png&s=44363)
上面文件中{}包裹的地方叫做区块，这样的区块留给真正要实现的文件。这里只是作为一个基础模板来使用。
### 创建模板
在项目中创建views文件夹，所有的模板引擎都放到这里面。在views文件夹下建立index.html  并继承自loyout.html，内容如下：

![](https://user-gold-cdn.xitu.io/2019/1/25/16883c0cd17aadfe?w=683&h=294&f=png&s=27575)
在html中的区块相当于往继承过来的模板中填充代码。title这样的数据是从服务端传过来的数据。
### 服务端渲染
使用服务端渲染，渲染出模板，并且可以往模板中传递数据。
```javascript
app.get('/test',(req,res) => {
    res.rendr('index',{
        title : '测试首页',
        data : 'Hello express'
    })
})
```
这时index.html就可以修改一下。

![](https://user-gold-cdn.xitu.io/2019/1/25/16883c06915c2e9f?w=785&h=293&f=png&s=27366)
这样就可以取到值了。

> 注意：模板不要滥用，所用的数据都往前台输出，会造成模板和后台的压力很大，而把一些有必要的东西输出。