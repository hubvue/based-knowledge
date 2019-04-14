---
title: Parcel初体验及特性分析
layout: post
subtitle: 新型构建工具初探及特性分析
date: '2019-02-26 10:00:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- 前端工程化
- Parcel
---

### Parcel
Parcel是近年出现的新一代打包工具，它的出现很明显的要挑战Webpack，因为它官网的一句话：
> 极速零配置Web应用打包工具。

### Parcel的特性分析
官网把parcel的特性说的明明白白的。
![](https://user-gold-cdn.xitu.io/2019/2/24/1691d9304232d6e0?w=1013&h=323&f=png&s=65748)
1. 第一条：我们都知道webpack是基于node的，而node是单线程的，并且webpack解析资源文件是基于loader的，每一次经过loader解析都要经历String->AST->String的过程，所以webpack打包起来很慢。目前也有个叫happypack的工具帮助webpack使用worker进程进行多核编译。而Paecel本身就是使用worker进行启动多个编译，而且存在文件系统缓存，打包速度很快。
2. 第二条：Parcel是一个开箱即用的打包工具，它能开箱即用到什么程度，它可以使用任何类型的文件作为entry(当然最好还是使用html和js)，而且不需要插件。相对于webpack只能使用js作为entry，真的是好太多了。
3. 第三条：关于babel、postcss、posthtml相关的东西，parcel不需要任何配置，只需要在项目目录下创建.babelre、.postcssrc、.posthtmlrc并且书写即可。parcel会自动的去找这些文件。但是这也有不好的地方，如果下载的包里面带有.babelrc的话，parcel也会将那个.babelrc作为本项目中的文件来处理。
4. 第四条：只要使用动态import()语法，Parcel就将输出的文件进行拆分。
5. 第五条：Parcel不需要配置，自身就集成了HMR(热更新),方便在开发模式下使用。
6. 第六条：友好的错误日志，当遇到错误的时候，Parcel会把错误代码并且高亮的在控制台显示出来。

### 使用
>使用parcel node版本必须要在8以上

#### 安装Parcel
> npm install -g parcel-bundler

项目目录结构如下：
```
Parcel-demo
├── src                   静态资源
    ├── scripts                 js文件
        └── index.js
        └── main.js
    ├── styles                  css文件
        └── main.css
    ├── image                  img文件
        └── background.jpg
├── node_modules            
└── index.html                  html文件
└── package.json            
```
内容如下：

index.js中引入main.js
```javascript
import main from './main';
main();
```
main.js中引入main.css
```javascript
import classess from '../styles/main.css'
export default ()=> {
    console.log('main init');
}
```
main.css中使用background.jpg
```css
body{
    background:url('../image/backgroun.jpg');
}
```
index.html中引入index.js

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script src="./src/scripts/index.js"></script>
</body>
</html>
```
就这样把所有的静态资源串起来。
#### 启动打包
> parcel index.html

![](https://user-gold-cdn.xitu.io/2019/2/24/1691dc918835340f?w=487&h=60&f=png&s=12084)
可以看到parcel自身继承了server。并且文件中多出了两个文件夹：dist和.cache
![](https://user-gold-cdn.xitu.io/2019/2/24/1691dca4ee057cff?w=167&h=236&f=png&s=10203)
1. .cache是打包缓存。
2. dist是打包输出的文件。

![](https://user-gold-cdn.xitu.io/2019/2/24/1691dcaf3bea2d5b?w=325&h=138&f=png&s=8612)
可以看出parcel自动的把打包后的文件添加了md5戳。

#### 使用babel
使用babel编译ES6只需要在项目根目录中创建.babelrc文件并书写配置即可。
```json
{
    "presets": [
        "@babel/preset-env"
    ]
}
```
运行parcel。

![](https://user-gold-cdn.xitu.io/2019/2/24/1691dd06305f398d?w=796&h=299&f=png&s=57066)
成功了。

更多的[转换](https://zh.parceljs.org/transforms.html)

#### 代码拆分-按需引入
parcel集成了使用import动态引入就可以达到代码拆分的效果。

新建test.js，代码入下：
```javascript
export default ()=>{
    console.log('test init');
}
```
在index.js中动态引入：
```javascript
import main from './main';
class Test{
    constructor(){
        console.log("test init");
    }
}
main();

+import("./test").then((_)=>{
+    _.default();
+})
```
启动编译我们会发现dist中多了一个文件。

![](https://user-gold-cdn.xitu.io/2019/2/24/1691dd6fc946bca8?w=336&h=185&f=png&s=10207)

打开index.html查看控制台。
![](https://user-gold-cdn.xitu.io/2019/2/24/1691dd7ba73ee6c1?w=619&h=47&f=png&s=3561)
成功了。

#### parcel的命令
##### server
serve 命令启用一个开发服务器，且支持 热模块替换 以实现快速开发。当你更改文件时，该服务器将自动重新构建你的应用程序。
> parcel index.html

##### build
build 命令会一次性构建资源，它还启用了压缩功能，并将 NODE_ENV 变量设置为生产环境。
> parcel build index.html

##### watch
监听（watch）命令与服务器类似，主要区别在于它并不会启动服务器。
> parcel watch index.html

更多的[命令](https://zh.parceljs.org/cli.html)

### Parcel与Webpack的比较
就上面小例子而言，就可以看出，Paecel是不可以控制输出文件的目录的，虽然特性居多，但是大型项目中的构建使用它是比较麻烦的。webpack虽然配置起来比较麻烦，但是更多的是可控，更有利于构建大型项目。所以说构建工具之多，使用之前一定要结合实际进行工具选型。

这里有一篇《深入浅出Webpack》的作者吴浩麟写的一篇关于[Webpack和Paecel的比较](https://juejin.im/post/5a439fd7518825455f2f91a7)可以参考，更够帮助在构建项目的时候正确的使用构建工具。