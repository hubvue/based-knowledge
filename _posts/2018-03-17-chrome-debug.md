---
title: 前端开发高级调试技巧
layout: post
subtitle: 使用Chrome控制台调试前端代码
date: '2019-03-17 15:02:27'
author: wang chong
catalog: true
header-img: "/img/bg/hello_world.jpg"
tags:
- 性能优化
---

### 断点以及捕获事件的绑定
#### 断点
`断点代码调试`方式在编辑器中也可以调试，本次采用Chrome控制台调试。首先打开我们要调试的网站，按`F12`会弹出浏览器控制台或者右键点击打开审查元素，会看到这样一个界面。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a613b6c1cc51?w=549&h=638&f=png&s=106738)
最上面一层是导航，其中第三个就是存放源代码的地方。打开
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a628b4e8bc4f?w=768&h=461&f=png&s=50069)
然后寻找到所要调试的文件，例如我们找到index。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a63cd0ce4318?w=775&h=607&f=png&s=145734)
点击代码左边这一排数字，就可以对每一行代码打上断点。

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a697fa469ee8?w=991&h=607&f=png&s=160031)
可以看到打上断点可以在右边栏中的Breakpoints中标记出来。然后按下F5刷新。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a6a21c5b0d9c?w=380&h=257&f=png&s=7637)
此时网站就进入了调试状态。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a6b09f06b358?w=434&h=67&f=png&s=5476)
右上角的这些按钮可以控制调试的进行。

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a6bb279e523b?w=415&h=119&f=png&s=12512)
Call Stack表示此时代码运行在什么环境下(全局还是局部)

Scope表示此时的作用域。

#### 寻找事件监听
这种方式可以找到指定元素绑定的事件。

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a6ebab6eb324?w=494&h=365&f=png&s=28520)
上面选中了button，会弹出有关button的相关属性
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a6ff21c5c2f8?w=583&h=392&f=png&s=61386)
点击`Event LIsteners`，可以查看这个元素绑定了哪些事件
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a707c4b170b9?w=596&h=153&f=png&s=14035)

#### DOM元素断点
DOM元素也是可以打断点的，在Elements里面找到指定的DOM元素，右键点击选择`Break on`可以设置DOM元素断点。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a72651876032?w=397&h=348&f=png&s=27629)
打过断点之后会有一个蓝色的小点点。

DOM元素断点的方式有三种：
- node removal --- DOM元素移除 
- attribute modifications --- DOM元素的attribute 改变
- subtree modifications --- DOM的子树改变