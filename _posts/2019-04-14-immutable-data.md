---
title: 数据的不可变性
layout: post
subtitle: React中使用数据的不可变性
date: '2019-04-14 11:08:41'
author: wang chong
catalog: true
header-img: https://user-gold-cdn.xitu.io/2019/4/14/16a198b58818e7c9?w=613&h=575&f=gif&s=317660
tag:
- React
---

## 不可变性
上个文章中有提到，当数据改变后是一新的对象和原本的对象没有关系，不就可以比较了。这种方式有两种实现方式。

### Clone
克隆分为浅克隆和深克隆：
- 浅克隆：只是把对象的第一层属性克隆下来，如果内部有对象或者数组的话则不会再继续克隆。
- 深克隆：深度克隆一个对象，如果一个对象内部还有一个对象的话，则继续克隆。深度克隆除了值相同其他没有任何联系。

对于PureComponent我们需要使用深克隆。但是你有没有想过，当我只是改变对象的一个属性的时候，需要把所有的属性全部都克隆一遍，会浪费很多内存，并且深度克隆的时间更长。因为优化这一点，反而浪费更多的空间和时间，这是得不偿失的。

### Immutable
那么，如果我在改变一个对象的时候，只是改变需要改变的值，把没有改变的值全部都共享下来，是不是就可以解决克隆所带来的问题？Immutable就是为这而生。

Immutable是一个基于函数式编程的库，Facebook致力于3年时间把这个库打造出来。

Immutable采用一种共享引用的方式，只会改变改变节点数据的那个分支的节点，其他分支节点空想引用。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a198b58818e7c9?w=613&h=575&f=gif&s=317660)

#### 使用方式
安装Immutable包
> npm install immutable --save

在项目中引入
> import { List } from "immutable";

使用immutable的List，只需把数据包裹起来即可。
```js
let temps = List([
    {
        message : "12",
    }
])
```
Immutable封装的数据类型有自己的一套获取数据和改变数据的方法。详情请查看[immutable官网](http://immutables.github.io/)

#### Immutabl不足之处
对于项目来说，Immutable确实解了实际上的问题，并且也方便了开发。但是Immutable封装的是一套新的数据结构，目前有太多的数据结构(obj,array,set,map)，再学习起来其他的很容易混乱，因此回到根本才是最佳的选择。

### 回到根本，最佳实践。
React中的状态数据我们通常使用两种方式来存储，对象和数组。其中对于我自己来说数组作为list很多。使用数据的一方法也可以做到数据的不可变性。
#### 数组
##### 增加
使用数组增加一项我们通常使用的是push方法。根据数据的不可变性，它肯定不能满足需求，它改变了数据，并且我们通常是把方法的返回值作为值赋予新的状态。而push方法返回的是添加的那个值，并不是一个新的数组。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a199e0f1486568?w=388&h=99&f=png&s=5228)

可以考虑使用concat方法，concat方法用于两个两个数组，并返回一个新的数组。可以满足需求。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a199fc9a2e5afd?w=341&h=42&f=png&s=4077)
##### 删除
通常使用数组的删除操作都是使用pop或者shift方法，它们依然改变了数据不能满足需求。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a19a32f35eff78?w=270&h=81&f=png&s=3035)
我通常的做法是使用filter过滤，知道需要删除的数据是哪个或者知道数据的索引就可以把数据给过滤掉。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a19a4b77a8c253?w=443&h=43&f=png&s=4708)

##### 更新
更新的方式就有很多了，通常使用map方法，可以通过map方法找到需要更新的值操作并且map方法是纯的，不会对原有数据造成影响。
![](https://user-gold-cdn.xitu.io/2019/4/14/16a19a7dcb62e3f5?w=473&h=43&f=png&s=6727)

#### 对象
对于对象来说，方法基本上都是改变原数据的，可以采用一些ES6的新特性和借助数组的方法实现不可变性。
##### 增加
对象的增加，可以采用ES6对象的扩展运算符。
![](https://user-gold-cdn.xitu.io/2019/4/14/16a19ab07e6fb44c?w=526&h=83&f=png&s=9768)

##### 删除
删除操作借助数组的reduce方法。
```js
var temp = {
    name: "zhangsan",
    age: 18,
    sex: "man",
}
Object.keys(obj).reduce((obj,key) => {
    if(key !== 'sex'){
        return {...obj,[key]: temp[key]}
    }
    return obj;
},{})
```

![](https://user-gold-cdn.xitu.io/2019/4/14/16a19b32214ef931?w=467&h=183&f=png&s=19321)

##### 更新操作
更新操作采用ES6的`Object.assign`方法，它的作用相当于merge。
```js
var temp = {
    name: "zhangsan",
    age: 18,
    sex: "man"
}
Object.assign({},temp,{
    age: 20,
})
```
![](https://user-gold-cdn.xitu.io/2019/4/14/16a19b53f7711e26?w=617&h=141&f=png&s=14506)

#### 总结
从上面这些原生的方法可以看出，合理是使用js原生的方法对我们写代码还是有很大好处的，并且原生的方法和再次封装的数据结构相比执行效率肯定是更快的。