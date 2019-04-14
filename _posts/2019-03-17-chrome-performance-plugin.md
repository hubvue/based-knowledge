---
title: 前端开发中的Chrome性能监控插件
layout: post
subtitle: 多种前端开发性能监控插件
date: '2019-03-17 15:43:42'
author: wang chong
catalog: true
header-img: "/img/bg/hello_world.jpg"
tags:
- 性能优化
---

### Audits面板
Audits是性能监控中的合成监控。
> 所谓合成监控就是计算机可以模拟网站走一边，实现简单，可以对渲染中的每一帧进行截图。


![](https://user-gold-cdn.xitu.io/2019/3/17/1698a7c8009cd61d?w=939&h=576&f=png&s=48648)
上面选项可以选择Desktop或者Mobile进行监控，点击`Run audits`就可以进行监控了。

但是这个有一点不好的是，监控出来的报告是英文的，对于英文不好的来说就不易阅读。

还有一个Chrome插件叫做`Lighthouse`和`Audits`是一模一样的功能。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a808eca07fc2?w=395&h=222&f=png&s=17420)
点击`Generator report`就可以进行监控了。

我们来对百度实验一下。

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a81d103b2a88?w=1349&h=638&f=png&s=84326)
(英文....emmm，可以右键点击翻译一下)

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a82c568eeb99?w=1284&h=618&f=png&s=93015)
这样就舒服多了，可以看出这个插件对性能的覆盖率是挺大的，功能也很全，以及每一项性能还提供了可优化的策略。


### Performance-Analyser
这个插件可以直观的显示出网站有多少个请求，多少个域，各种渲染时间等等，并且还提供图标分析。

依然为百度为例
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a8559cc8296c?w=1298&h=473&f=png&s=46596)

![](https://user-gold-cdn.xitu.io/2019/3/17/1698a858ecd051a4?w=1294&h=377&f=png&s=51690)
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a85ed886c57b?w=1290&h=459&f=png&s=41177)

### Page Speed
这个是比较老的一个性能监控的插件。这个插件可以检测出在性能优化那么多条例里面([雅虎军规](http://blog.ctomorrow.top/2019/03/03/yahoo/))页面中有多少是做到的，有多少是没做到的。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a88a6318454c?w=957&h=404&f=png&s=59172)
点击`START`进行检测。
![](https://user-gold-cdn.xitu.io/2019/3/17/1698a89250c93c68?w=952&h=555&f=png&s=73107)

### 总结
性能监控的插件之多，如果选择使用主要还是要看你要去检测什么。比如说我的网站各项指标做的怎么样可以使用`Audits`或者`Lighthouse`来检测；我想查看我的网站多少个请求，各项指标的图标分析，可以使用`Performance-Analyser`；我想查看我的网站所践行的雅虎军规，有多少我是已经做到的，有多少没做到的可以使用`Page Speed`这个比较老牌的插件。