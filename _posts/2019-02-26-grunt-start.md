---
title: 学习Grunt构建方式
layout: post
subtitle: Gunt入门学习
date: '2019-02-26 09:30:00'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- 前端工程化
- Grunt
---

### Grunt
Grunt是和Gulp在同一个时期的前端自动化构建工具。但是因为Gulp编译的速度比Grunt更快，所以在那个时期就被Gulp打压。虽说并没有想象中的那么火，但是在前端工程化的道路上，还是有必要学习一下它的。

### 何为构建工具？
> 一句话：自动化。对于需要反复重复的任务，例如压缩（minification）、编译、单元测试、linting等，自动化工具可以减轻你的劳动，简化你的工作。

在前端领域由于NodeJS的兴起，让前端开始有了工程化，项目中后端大多数东西前移，前端变的繁重，书写起来也越来越麻烦。自动化构建工具的出现，让前端更加顺手的使用模块化，对于批量处理的文件构建工具可以一键完成。

### 为什么要使用Grunt?
可能现在前端构建工具有很多了，gulp和webpack等历史的红人和现代的红人统治者构建世界，其实我个人在学习过程中并没有感觉到Grunt相对于其他的优点。引用官网的一些话。

> Grunt 生态系统非常庞大，并且一直在增长。由于拥有数量庞大的插件可供选择，因此，你可以利用 Grunt 自动完成任何事，并且花费最少的代价。如果找不到你所需要的插件，那就自己动手创造一个 Grunt 插件，然后将其发布到 npm 上吧。

### 开始使用Grunt
#### 安装grunt-cli
首先需要把`grunt-cli`安装到全局变量中(当然安装在项目中也可以，只是安装在全局使用比较顺手)，`grunt-cli`主要是用于把`grunt`命令加入到系统路径中，以后在任何路径就都可以使用了。
> npm install grunt-cli -g

##### grunt-cli工作
当每次运行`grunt`的时候，grunt-cli就会利用node提供的`require()`系统查找本地安装的Grunt。这样的好处是：1、不需要我们自己引入`Grunt`,2、可以在项目的任意子目录中运行`grunt`

#### 项目目录分布
我们来使用一个简单的项目来使用一下Grunt
```
grunt-demo
├── build                  grunt编译的文件
├── dist                   合并上线的文件
├── node_modules            
├── src                    源文件
    ├── scripts               JavaScript文件
        └── index.js  
        └── main.js  
└── index.html             HTML文件
└── gruntfile.js           Grunt配置文件
└── package.json            
```
#### 编写gruntfile.js
Grunt是运行时基于任务的，它插件系统非常强大，每个插件可以看做一个任务。
1. 导出函数的参数注入grunt对象
2. grunt.initConfig方法初始化任务
3. grunt.registerTask('default',[]) 默认执行的任务列表

```javascript
//参数注入grunt对象
module.exports = function(grunt){
    //grunt初始化任务
    grunt.initConfig({
        pkg : grunt.file.readJSON('package.json'),
    })
    //默认执行的任务列表
    grunt.registerTask('default',[]);
}
```
##### uglify任务
uglify任务用于压缩JavaScript文件。使用方式如下
```javascript
module.exports = function(grunt){
    grunt.initConfig({
        pkg : grunt.file.readJSON('package.json'),
        //配置uglify插件
+        uglify : {              //压缩
+            options : {
+                //在文件的顶部添加日期
+                banner : '/*! create by <%= grunt.template.today("yyyy-mm-dd")%>*/\n'
+            },
+            //找到静态资源对应的目录，指定静态资源从哪来到哪去
+            static_mappings : {
                //所有的文件在这里配置
                    //src：入口文件
                    //build：出口文件
+                files : [{
+                    src : "src/scripts/index.js",
+                    dest : "build/index.min.js"
+                },{
+                    src: "src/scripts/main.js",
+                    dest : "build/main.min.js"
+                }],
+            }
+        },
    });
    //加载插件
+   grunt.loadNpmTasks('grunt-contrib-uglify');
    //把任务调价到默认执行任务队列中
    grunt.registerTask('default',['uglify']);
}
```
这个时候我们还没有装`grunt-contrib-uglify`插件，我们来安装一下
> npm install grunt-contrib-uglify --save-dev

执行grunt
![](https://user-gold-cdn.xitu.io/2019/2/23/1691aeb55a3f1a0e?w=156&h=74&f=png&s=2886)
build中生成了两个文件。

打开index.min.js和main.min.js
![](https://user-gold-cdn.xitu.io/2019/2/23/1691aec2d716b4f8?w=651&h=80&f=png&s=15690)
![](https://user-gold-cdn.xitu.io/2019/2/23/1691aec5019001e5?w=713&h=94&f=png&s=18346)
成功了。

##### concat任务
concat任务用于合并JavaScript文件。
```javascript

module.exports = function(grunt){
    grunt.initConfig({
        pkg : grunt.file.readJSON('package.json'),
        uglify : {              //压缩
            options : {
                //在文件的顶部添加日期
                banner : '/*! create by <%= grunt.template.today("yyyy-mm-dd")%>*/\n'
            },
            //找到静态资源对应的目录，指定静态资源从哪来到哪去
            static_mappings : {
                files : [{
                    src : "src/scripts/index.js",
                    dest : "build/index.min.js"
                },{
                        src: "src/scripts/main.js",
                    dest : "build/main.min.js"
                }],
            }
        },
        //注册concat任务
+        concat : {
+            //src需要合并的文件
+            //合并之后的文件
+            bar : {
+                src : ['build/*.js'],
+                dest : 'dist/all.min.js',
+            }
+        },
    });
    //加载插件
    grunt.loadNpmTasks('grunt-contrib-uglify');
+    grunt.loadNpmTasks('grunt-contrib-concat');
    //默认被执行的任务列表
    grunt.registerTask("default", ['uglify','concat'])
}
```
安装`grunt-contrib-concat`插件
> npm install grunt-contrib-concat --save-dev

执行grunt
![](https://user-gold-cdn.xitu.io/2019/2/23/1691aef116eb70d5?w=161&h=50&f=png&s=1777)
项目中生成了dist目录并且其中有all.min.js文件，打开这个文件
![](https://user-gold-cdn.xitu.io/2019/2/23/1691aefad289d0ba?w=780&h=158&f=png&s=32747)
合并成功。

##### watch任务
在开发项目的过程中，本地开发肯定是最多的，watch任务主要用于本地监听文件的变化，不需要一次一次的grunt重启。
```javascript

module.exports = function(grunt){
    grunt.initConfig({
        pkg : grunt.file.readJSON('package.json'),
        uglify : {              //压缩
            options : {
                //在文件的顶部添加日期
                banner : '/*! create by <%= grunt.template.today("yyyy-mm-dd")%>*/\n'
            },
            //找到静态资源对应的目录，指定静态资源从哪来到哪去
            static_mappings : {
                files : [{
                    src : "src/scripts/index.js",
                    dest : "build/index.min.js"
                },{
                        src: "src/scripts/main.js",
                    dest : "build/main.min.js"
                }],
            }
        },
        concat : {
            bar : {
                src : ['build/*.js'],
                dest : 'dist/all.min.js',
            }
        },
+        watch : {
+            //观察文件
+            files : ['js/index.js'],
+            //执行任务
+            tasks : ['uglify','concat']
+        }
    });
    //加载包含‘uglify’任务的插件。
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-concat');
+    grunt.loadNpmTasks('grunt-contrib-watch');
    //默认被执行的任务列表
    grunt.registerTask("default", ['uglify','concat','watch'])
}
```
安装`grunt-contrib-watch`插件
> npm install  grunt-contrib-watch --save-dev

启动grunt

![](https://user-gold-cdn.xitu.io/2019/2/23/1691af5f77cf7ebb?w=635&h=223&f=png&s=32301)
启动之后会发现项目不会停止处于监听状态。

#### 更多的插件
grunt的插件系统很庞大，更多的请[点击](https://www.gruntjs.net/plugins)