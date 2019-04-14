---
title: 记一次webpack配置文件的分离
layout: post
subtitle: 记一次webpack配置文件的分离思考
date: '2019-01-23 13:16:37'
author: wang chong
header-img: /img/bg/hello_world.jpg
catalog: true
tags:
- webpack
---

随着前端技术的发展，业务逻辑的增多及功能化的繁琐已经成为前端人员最烧脑的问题。前端自动化构建工具的出现，为前端人员带来了项目构建上的福音，成为每个前端工程师必回的技术栈，目前比较流行的Webpack以万物皆模块的思想构建我们的前端项目，同样也是笔者正在使用的一个前端自动化构建工具。

Webpack对于每个前端人员来说都不会怎么陌生，它将每一个静态文件当做一个模块，经过一系列的处理为我们整合出最后的需要的js、css、图片、字体等文件。

### 单个配置文件所造成的问题
本文默认电脑前的你已经了解一些Webpack基础的配置，并懂得了webpack.config.js配置文件的基础搭建。

随着我们业务逻辑的增多，图片、字体、css、ES6以及CSS预处理器和后处理器逐渐的加入到我们的项目中来，进而导致配置文件的增多，使得配置文件书写起来比较繁琐，更严重者（书写特定文件的位置会出现错误）。更由于项目中不同的生产环境和开发环境的配置，使得配置文件变得更加糟糕。

使用单个的配置文件会影响到任务的可重用性，随着项目需求的增长，我们必须要找到更有效地管理配置文件的方法。

### 管理配置文件的几种方法
配置文件的管理有一下几种方法。

* 在每个环境的多个文件中维护配置，并通过--config参数将webpack指向每个文件，通过模块导入共享配置。
* 将配置文件推送到库，然后引用库。
* 将配置文件推送到工具。
* 维护单个配置文件的所有配置并在那里进行分支并依赖--env参数。

本文以第四种方式阐述webpack配置文件的分离。

### 分离配置文件

我们在根目录下创建config文件夹，并创建四个配置文件，分别是：
* webpack.comm.js   公共环境的配置文件
* webpack.development.js    开发环境下的配置文件 
* webpack.production.js     生产环境下的配置文件
* webpack.parts.js      各个配置零件的配置文件

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e44eeb0eb185?w=234&h=111&f=png&s=5949)

### 合并配置文件的工具
如果配置文件被分成了许多不同的部分，那么必须以某种方式来组合他们，通常就是合并数组和对象，webpack-merge很好的做到了这一点。
> webpack-merge做了两件事：它允许连接数组并合并对象，而不是覆盖组合。

```
const merge = require("webpack-merge");
merge(
    {a : [1],b:5,c:20},
    {a : [2],b:10, d: 421}
)
//合并后的结果
{a : [1,2] ,b :10 , c : 20, d : 421}
```

### 使用webpack-merge合并配置文件
首先将webpack-merge添加到项目中
```CMD
npm install webpack-merge --save-dev
```
首先设置各个配置文件的连接
#### webpack.config.js
```JavaScript
const commConfig = require("./config/webpack.comm");
const developmentConfig = requie("./config/webpack.development");
const productionConfig = require("./config/webpack.development")
const merge = require("webpack-merge");

module.exports = mode => {
    if(mode === "production"){
        return merge(commConfig,productionConfig,{mode});   
    }
    return merge(commConfig,developmentConfig,{mode});
}
```
上面代码利用mode的值来判断是开发环境还是生产环境
#### webpack.comm.js
```JavaScript
const merge = require("webpack-merge");
const parts = require("./webpack.parts")    //引入配置零件文件
const config = {
    //书写公共配置    
}
module.exports = merge([
    config,
    parts......
])
```
#### webpack.production.js
```JavaScript
const merge = require("webpack-merge");
const parts = require("./webpack.parts");   //引入配置零件文件
const config = {
    //书写公共配置
}
modules.exports = merge([
    config,
    parts......
])
```
#### webpack.development.js
```JavaScript
const merge = require("webpack-merge");
const parts = require("./webpack.parts");   //引入配置零件文件
const config = {
    //书写公共配置
}
modules.exports = merge([
    config,
    parts......
])
```
### 使用--env值传参
使用--env允许将字符串传递给配置。我们来修改下package.json
```
    "dev": "webpack --env development ",
    "prod": "webpack --env production",
    "dev:server": "webpack-dev-server --env development "
```
这样就使得env参数mode环境参数传入到webpack.config.js中，就可以判断是生产环境还是开发环境。

### 如何写出可配置的webpack.parts.js
上面可以看出我们新建了一个webpack.parts.js文件，这个文件中主要是存放我们的一些配置零件。如何写出可配置，可拔插的配置零件。就是我们这个文件的最重要的部分。

以loadCSS为例：
```Javascript
exports.loadCSS = ({reg = /\.css$/,include,exclude,uses = []} = {}) => ({
    module : {
        rules:[{
            test : reg,
            include,
            exclude,
            use[{
                loader : "style-loader",
            },{
                loader : "css-loader",
            }].concat(uses),
        }]
    }
})
```
上面代码中，利用exports导出单个配置零件，通过解构赋值来传入参数。使用数组的concat来连接外部导入的loader。参数解析：
* reg:表示loader匹配的test正则，默认为css，这里可以是（less、sass、stylus）。
* include:表示所要打包的文件夹。
* exclude：表示要跳过打包的文件夹。
* uses：外部导入的loader。

#### 在webpack.development.js中引入
```javascript
module.exports = merge([
    config,
    parts.loadCSS({
        reg : /\.less/,
        use : ["less-loader"]
    }),
    parts.loadCSS(),
])

```

### 分离配置文件的好处
配置文件拆分可以是我们继续扩展配置。最重要的收益是我们可以提取不同目标之间的共性。并且还可以识别要组合的较小配置部件，这些配置不见可以推送到自己的软件包以跨项目使用。还可以将配置作为依赖项进行管理，而不是在多个项目中复制类似的配置。
### 我自己的parts配置
展示一部分我自己的部件配置，由于在学习阶段，不足的地方还望大佬们提出，学习进步。
```javascript
/**
 * @name 本地服务器配置
 * @param host  打开的url
 * @param port  打开url的端口号
 * 
 */
exports.devServer = ({ host, port} = {}) => ({
    devServer : {
        stats : "errors-only",
        host,
        port,
        open : true,
        overlay : true,
    }
})
/**
 * @name 未从js中分离的cssLoader配置
 * @param reg 匹配文件的后缀名test
 * @param include 所要打包的文件夹
 * @param exclude 跳过打包的文件夹
 * @param uses 所要向loadCSS中添加的loader
 */
exports.loadCSS = ({reg = /\.css$/,include,exclude,uses = []} = {}) => {
    return {
        module: {
            rules: [{
                test: reg,
                use: [{
                    loader: "style-loader"
                }, {
                    loader: "css-loader"
                }].concat(uses),
                include,
                exclude,
            }]
        },
    }
}
/**
 * @name 从js中分离的cssLoader配置
 * @param reg 匹配文件的后缀名test
 * @param include 所要打包的文件夹
 * @param exclude 跳过打包的文件夹
 * @param uses 所要向loadCSS中添加的loader
 *  */
const MiniCssExtrectPlugin = require("mini-css-extract-plugin");
exports.extractCSS = ({reg = /\.css$/,include,exclude,uses = []} = {}) => {
    const plugin = new MiniCssExtrectPlugin({
        filename : "styles/[name]-[hash:5].css",
    })
    return {
        module: {
            rules: [{
                test: reg,
                use: [{
                    loader: MiniCssExtrectPlugin.loader,
                    options : {
                        publicPath : "../"
                    }
                }, {
                    loader: "css-loader"
                }].concat(uses),
                include,
                exclude,
            }]
        },
        plugins : [
            plugin,
        ]
    }
}
/**
 * @name css tree-shaking：将没有用到的css扔掉
 * @param paths 监听css tree-shaking 的文件名
 */
const PurifyCssPlugin = require("purifycss-webpack");
exports.purifyCSS = ({paths}) => ({
    plugins : [
        new PurifyCssPlugin({paths})
    ]
})

/**
 * @name autoprefixer 为css样式添加浏览器前缀
 * @author wangchong
 */
exports.autoprefix =() =>({
    loader : "postcss-loader",
    options : {
        plugins : () => [require("autoprefixer")]
    }
})

/**
 * @name loadImage ：打包图片资源
 * @param include 所要打包的文件夹
 * @param exclude 跳过打包的文件夹
 * @param options loader的options配置
 */
exports.loadImage = ({include,exclude,options} = {}) => ({
    module : {
        rules : [
            {
                test : /\.(png|jpg)$/,
                include,
                exclude,
                use : {
                    loader : "url-loader",
                    options,
                }
            }
        ]
    }
})

```
文章总结自：[Surviejs-webpack](https://survivejs.com/webpack/developing/composing-configuration/)。