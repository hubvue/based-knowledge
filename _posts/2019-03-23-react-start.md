---
title: React初探及环境搭建
layout: post
subtitle: React初探，React环境搭建，编写第一个HelloWorld
date: '2019-03-23 15:47:54'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

### 什么是React
React是Facebook开发的一款用来构建用户界面的js库，对于react来讲还可以应用到nodejs，做同构化应用。
对于React自己来说，React只做的是非常纯粹的View层。React结合自己庞大的组件库，形成了MVVM框架。
 
### React特性
#### Virtual DOM
Virtual DOM是一个模拟DOM树的JavaScript对象。React使用Virtual DOM 来渲染UI，同时监听Virtual DOM上数据的变化，并自动迁移到UI上。
 
#### state
React有一个叫做state的概念。state是状态，整个React都是通过状态来驱动的，只要状态变换，React就会驱动View变化，View变化就会启动VirtualDOM的diff算法，通过diff算法找到DOM元素最小的变化，从而实现最小的操作DOM元素。
 
#### props
props是React中的属性，通过属性可以做到父子组件间的通信。
 
#### JSX语法
JSX语法是REact定义的一种JavaScript语法扩展，类似于XML。jSX是可选的，在开发过程中也可以不使用JSX，使用JavaScript来编写React应用(建议使用)。
 
#### components
React是专注于View层开发的，View是基于组件的，每一个JSX是一个组件。组件化开发可以创建可复用的UI组件，提高开发效率。
 
### 编写第一个React ”Hello World“
#### 环境搭建
因为在写React的时候使用的是JSX语法，所以开发模式下必须要使用编译工具来把JSX语法编译成JS来执行。
 
可以使用的工具有很多，我自己是搭建的一个webpack来写React
 
**初始化项目**
> npm init -y
 
**安装Webpack**
> npm install webpack webpack-cli webpack-dev-server --save-dev
 
**安装一些相应的webpack插件**
> npm install html-webpack-plugin --save-dev 处理html

> npm install clean-webpack-plugin --save-dev  每次构建清理清空dist文件夹
 
 
**安装React**
> npm install react react-dom --save
 
**安装babel**
> npm install babel-loader @babel/core @babel/preset-env @babel/preset-react --save-dev
 
**当我们在写es6的时候需要用到`static`,安装一个es7class的插件**
> npm install @babel/plugin-proposal-class-properties --save-dev
 
插件安装成功，创建`webpack.config.js`内容如下：
```js
const HtmlWebpackPlugin = require("html-webpack-plugin");
const CleanWebpackPlugin = require("clean-webpack-plugin");
const path = require("path");
const {mode} = require("yargs-parser")(process.argv.slice(2));
module.exports = {
    output : {
        filename : "[name]-[hash:5].js",
        path : join(__dirname,"..","dist"),
        publicPath : "/"
    },
    optimization : {
        splitChunks : {
            cacheGroups : {
                vendors: {
                    test: /[\\/]node_modules[\\/]/,     //拆分第三方包
                    priority: -10,
                    name : "vendors",
                    chunks : "all"
                  },
            }
        },
        runtimeChunk : {
            name : "runtime"        //拆分runtime
        }
    },
    module : {
        rules : [
            {
                test : /\.(jsx|js)$/,
                use : "babel-loader",
                exclude : path.join(__dirname,'node_modules'),
            }
        ]
    },
    plugins : [
        new HtmlWebpackPlugin({
            src :"./index.html",
            template : "index.html"
        }),
        new CleanWebpackPlugin()
    ]
}
```
 
`.babelrc`内容如下：
```json
{
    "presets": ["@babel/preset-env","@babel/preset-react"],
    "plugins" : [
        "@babel/plugin-proposal-class-properties"
    ]
}
```
创建`src`文件夹，并在其中创建`index.js`文件、`conponents`文件夹。在component文件夹下创建App.js,内容如下：
```jsx
import React from "react";
import ReactDOM from "react-dom";

export default class App extends React.Component {
    render(){
        console.log("父组件render页面");
        return (
            <div>
                Hello World
            </div>
            
        )
    };
}
```
`index.js`中引入App.js，内容如下：
 
```js
import App from "./components/App.jsx";
import ReactDOM from "react-dom";
import React from "react";

ReactDOM.render(
    <App  />,
    document.getElementById("app")  //挂载到#app上
)
```
 
![](https://user-gold-cdn.xitu.io/2019/3/23/169a983d33940572?w=421&h=68&f=png&s=1860)

成功