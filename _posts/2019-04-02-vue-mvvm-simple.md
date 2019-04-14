---
title: 两种方式实现超简单的数据双向绑定
layout: post
subtitle: 探究第一步：两种方式实现超简单的数据双向绑定
date: '2019-04-02 08:54:13'
author: wangchong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

- Object.defineProperty
- Proxy/Reflect


#### Object.defineProperty
Object.definedPropery是ES5的一种方式，需要使用一个中间人角色，作为中间数据交换者。
```js
var input = document.querySelector("#input");
var obj = {};
var a;
Object.defineProperty(obj, 'a', {
    get: function () {
        return a;
    },
    set: function (newVal) {
        input.value = newVal;
        a = newVal;
    }
})
input.addEventListener('input', function (e) {
    obj.a = e.target.value;
    console.log(obj.a);
})
```
#### proxy/Reflect
proxy是ES6的API，相当于对于代理者进行一层拦截，然后通过Reflect反射回去。
```js
let objDate ={};
let temp = {};
const input = document.querySelector('#input'); let proxy = new Proxy(objDate,{
    set(target,key,value){
        if(key == 'data'){
            input.value = value;
            Reflect.set(target,key,value);
        }
    }
})
input.addEventListener('input',(e)=>{
    proxy.data = e.target.value;
    console.log(objDate.data);
})

```
![](https://user-gold-cdn.xitu.io/2019/4/2/169db89137a5dda5?w=1361&h=326&f=png&s=35148)
两种方式都可以实现上面的效果。