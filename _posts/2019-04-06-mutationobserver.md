---
title: 学习MutationObserver
layout: post
subtitle: 原生API-MutationObserver观察者
date: '2019-04-06 15:01:46'
author: wangchong
catalog: true
header-img: https://user-gold-cdn.xitu.io/2019/4/5/169ede6b800e4475?imageView2/1/w/1304/h/734/q/85/format/webp/interlace/1
tag:
- JavaScript
---

### MutationObserver
Mutation Observer API 用来监视DOM变动。DOM的任何变动，比如节点的增删、属性的变动、文本内容的变化，这个API都可以得到通知。

首先这个API归属于微任务，也就是说是异步的，这样设计也是为了应付Dom变动频繁的特点，防止频繁变动占用js执行栈造成页面卡顿。比如说：如果不是异步当DOM变动触发观察者的回调函数执行，就会堵塞后续代码的执行；是异步的话，在响应时间内比如说插入1000个p元素，那么MutationObserver会把这些响应合并成一次。

#### MutationObserver有以下特点：
1. 异步执行
2. 把异步时间内的DOM变动记录封装成一个数组处理，可以以数组的长度判断页面加载何时Dom跳跃大。
3. 它既可以观察DOM的所有类型变动，也可以指定只观察某一类类型的变动。

#### MutationObserver构造函数
早期的Firefox和Chrome版本中需要带有前缀。
```js
var MutationObserver = window.MutationObserver || window.WebkitMutationObserver || window.MozMutationObserver;
```
首先使用MutationObserver构造函数实例化一个MutationObserver对象，同时指定这个实例的回调函数。
```js
const observer = new MutationObserver(()=>{})
```
构造函数可以接受连个参数：
- 第一个参数为变动记录数据。
- 第二个参数为观察者实例。

```js
const observer = new MutationObserver((mutations,observer) => {
    console.log(muatation,observer);
})
```
#### MutationObserver实例
构造函数调用MutationObserver得到MutationObserver实例。实例有以下方法。
##### observe
observe方法用来监听Dom变化，接受两个参数：
- 第一个参数是所要观察的Dom节点
- 第二个对象是一个配置对象，指定所要观察的变动类型

```js
observer.observe(document.documentElement,{
    
})
```
Dom的变动类型一共有三种：
- childList : 子节点变动(指新增、删除、修改)。
- attributes ：属性的变动
- characterData ： 节点内容或节点文本的变动。

当变动类型为true的时候为监听状态，默认为不监听。

> 需要注意的是：必须同时指定三种变动类型中的一种或者多种，否则会报错。

除了三种变动类型外，对象属性还可以设置其他值：
- subtree ： 布尔值，表示是否将观察者应用于该节点的后代所有节点。
- attributeOldValue：布尔值，表示观察attributes变动时，是否需要记录变动前的属性值。
- characterDataOldValue：布尔值，表示观察characterData变动时，是否需要记录变动前的值。
- attributeFilter：数组，表示需要观察的特定属性(比如说['class','src']);

```js
observer.observe(document.documentElement,{
    childList : true,
    attributes : true,
    characterData : true,
    subtree : true,
    attributeOldValue: true,
    characterDataOldValue: true,
})
```
##### taskRecoreds
taskRecoreds方法用于清楚变动记录，即不再处理未处理的变动。该方法返回变动记录的数组。
```js
const changes = observer.taskRecords();
console.log(changes);
```
##### disconnect
disconnect方法用来停止观察。调用该方法后，DOM再发生变动，也不会触发观察者对象。
```js
observer.disconnect();
```
#### MutationRecord对象
DOM每次发生变化,就会生成一条变动记录(MutationRecord实例)。该实例包含了与变动相关的所有信息。MutationObserver处理的就是一个个MutationRecord实例组成的数组。

MutationRecord包含了Dom的有关信息，有以下属性：
- type：观察变动的类型(attribute、characterData或者childList)
- target : 发生变动的DOM节点
- addedNodes ：新增的DOM节点
- removedNodes ： 删除的DOM节点。
- previousSibling ： 前一个同级节点，如果没有则返回null。
- nextSibling ： 下一个同级的节点，如果没有则返回null。
- attributeName：发生变动的属性名，如果设置了attributeFilter，则只返回attributeFilter中的属性值。
- oldValue：这个属性只对attribute和characterData变动生效，如果发生childList变动，则返回null。

![](https://user-gold-cdn.xitu.io/2019/4/5/169edd7e05ef0632?w=526&h=266&f=png&s=24677)

### 总结
MutationObserver是异步操作，属于微任务，在Vue源码中实现`nextTick`的调度机制使用到了这个。
![](https://user-gold-cdn.xitu.io/2019/4/5/169eddb64e5b28c6?w=743&h=286&f=png&s=66045)
Vue中主要起到一个实现异步操作的调度机制，并没有真正体现到监听Dom变化的特点。

我个人这个东西在监听Dom变化上还是有大作用的。我们在做性能监控的时候，通常会检测一些性能指标，例如：`FP、FCP、FMP`这些性能指标。`FMP`的标准定义不明确，通常是指Dom活动最大的时刻。所以说`FMP`比较难检测，通常采用body前后打`mark`的方式检测。使用这个API的话，可以检测哪个时间段Dom到文本中最多，也就可以说成活动最大。

### 最后
这是[Can I Use](https://caniuse.com/#search=MutationObserver)上的MutationObserver API的支持度。
![](https://user-gold-cdn.xitu.io/2019/4/5/169ede5451dd1124?w=1586&h=575&f=png&s=79233)