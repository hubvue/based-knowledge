---
title: Vue组件与异步获取数据的方式
layout: post
subtitle: Vue定义组件，异步获取数据
date: '2019-03-31 17:40:18'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

### 定义一个组件
声明组件使用Vue.component方法，这个方法有两个参数，一个是定义组件的名字，另一个是一个对象，相当于实例化Vue所传入的对象。
```js
Vue.component('my-app',{
    template : "<h1>Hello Vue</h1>"  //template 是组件模板
})
```
上述就定义好了一个组件，使用直接在把`<my-app></my-app>`放在html中就可以。
```html
<my-app></my-app>
```
上面这种定义组件的方式，template属性中写的是字符串，并不能很好的书写，所以可以卸载template元素里面。template元素不会被html解析。
```html
<my-app></my-app>
<template id="template">
    <h1> Hello Vue</h1>
<template>
<script>
Vue.component('my-app',{
    template : "#template"  //这里写template元素的id
})
</script>
```
这两个是相同的效果，只是第二种相对于第一种写html比较方便。

### Vue异步获取数据的方式
异步获取数据最直接的方式就是我们最接地气的jQuery，引入jQuery使用ajax异步获取数据。

Vue有一个叫做`vue-resourece`，可以用来帮助我们发送请求。

> 脚手架安装：npm install vue-resource --save

> cdn :https://cdn.staticfile.org/vue-resource/1.5.1/vue-resource.min.js

> 使用插件 Vue.use(Resource);


#### 使用方式：
在vue实例中使用`this.$http.methods`就可以获取，methods是http的方法。

例如：http://jsonplaceholder.typicode.com/posts  来请求一个这个接口
```js
const vm = new Vue({
    el : "#app",
    data : {
        datas : [],
    }
    methods : {
        requests(){
            const _self = this;
            this.$http.get("http://jsonplaceholder.typicode.com/posts").then(res=>res.jons()).then(data => {
                _self.datas = data;
            }).catch(()=>_self.datas = []);
        }
    }
})
```
![](https://user-gold-cdn.xitu.io/2019/3/31/169d31bdc3cb7128?w=581&h=505&f=png&s=71568)

### 其他
还有一个插件是`vue-async-data`也是vue异步获取的插件。对于我个人来说我还是喜欢使用原生API的`fetch`,因为现在浏览器对fetch支持的很好，使用node的时候，服务端有`node-fetch`前后，node-fetch是服务端的fetch，二者API相同，使用起来比较顺手。