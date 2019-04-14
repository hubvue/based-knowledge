---
title: 学习browserify
layout: post
subtitle: browserify入门学习
date: '2019-02-26 09:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- 前端工程化
- Browserify
---

### browserify
browserify可以说是一个打包工具，它的最大的优点就是在项目中使用require等node模块，使用browserify打包之后可以在浏览器中运行。

### 安装
> npm install browserify -g

### 使用
#### 初始化项目
> npm init

创建index.html、index.js、test.js,内容如下。

```
//index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="./index-o.js"></script>
</head>
<body>
    
</body>
</html>
//index.js
var cheerio = require('cheerio');
var test = require("./test");

test(cheerio);
//test.js
module.exports = function(cheerio){
    console.log("我是cheerio", cheerio);
}
```
这里我们使用了cheerio库，它相当于服务端的jquery，而且我们使用了commonJS的模块化。

安装cheerio
> npm install cherrio --save

#### 使用browserify编译
> browserify index.js -o index-o.js

完成之后会发现项目中存在index-o.js
![](https://user-gold-cdn.xitu.io/2019/2/24/1691b43f793721a6?w=200&h=210&f=png&s=9194)
打开浏览器测试一下。

![](https://user-gold-cdn.xitu.io/2019/2/24/1691b4470ace5ab4?w=449&h=139&f=png&s=14260)
输出。

我们来打开index-o.js看一下。
![](https://user-gold-cdn.xitu.io/2019/2/24/1691b451bd539d58?w=1132&h=647&f=png&s=185540)
看到这一步我是惊呆了，我写了那么短的代码，为什么打包了那么多。足足有27809行代码。所以说这个打包工具有利也有弊的，自我感觉是弊大于利。