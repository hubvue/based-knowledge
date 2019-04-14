---
title: Vue组件缓存：Keep-alive
layout: post
subtitle: Vue keep-alive组件缓存
date: '2019-04-02 07:53:18'
author: wangchong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

### 页面缓存
在Vue构建的单页面应用(SPA)中，路由模块一般使用vue-router。vue-router不保存被切换组件的状态，也就是说，当组件切换的时候，旧组件会被销毁，而新组件会被建立，走一遍完整的生命周期。

但有时候，我们有这样一个需求，比如跳转到详情页面时，需要保持列表页的滚动条的深度，等返回的时候依然在这个位置，这样可以提高用户体验。在Vue中，对于这种`页面缓存`的需求，我们可以使用`keep-alive`组件来解决这个问题。

### 使用方式
首先搭建一个Vue应用。这里是使用vue-cli搭建的一个简单的Vue应用，App.vue内容如下：
```vue
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <keep-alive>
      <router-view />
    </keep-alive>
    <router-view />
  </div>
</template>

<script>
import Foo from './hooks/index';
export default {
  name: 'App',
  components :{
    "hooks-index" : Foo,
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```
使用vue-router做了两个简单的跳转，上面代码为了演示清晰，使用两个router-view，一个是使用`keep-alive`的，一个没有使用。router内容如下：
```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import TestKeep from '@/components/TestKeep'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/keepAlive',
      name: 'TestKeep',
      component: TestKeep
    }
  ]
})
```
主页HelloWorld.vue内容如下：
```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input type="text" placeholder="keepAlive" v-model="msg">
    <router-link to="/keepAlive">跳转</router-link>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: "keepAlive"
    }
  }
}
</script>
```
缓存页TestKeep.vue内容如下：
```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input type="text" placeholder="keepAlive" v-model="msg">
    <router-link to="/">跳转</router-link>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: "keepAlive"
    }
  }
}
</script>
```
首先是在Test-keep组件中，什么也没有输入的状态
![](https://user-gold-cdn.xitu.io/2019/4/2/169db3da258e6e8b?w=389&h=321&f=png&s=14231)
然后我在每个文本框中输入一些东西。
![](https://user-gold-cdn.xitu.io/2019/4/2/169db3e5a9541755?w=344&h=435&f=png&s=20517)
然后点击跳转回到首页
![](https://user-gold-cdn.xitu.io/2019/4/2/169db3f16aa0b36a?w=345&h=459&f=png&s=20293)
再次点击跳转到Test-keep组件中
![](https://user-gold-cdn.xitu.io/2019/4/2/169db3f7821a6abd?w=376&h=477&f=png&s=21564)
现在我们可以清楚的看到第一个文本框内容依然存在，第二个文本框中的内容被初始化了。

### activated钩子函数
这个钩子函数是当页面中有组件缓存`keep-alive`的时候，触发钩子函数执行。

我们来定义一些列的钩子函数。
```js
  activated(){
    console.log('组件缓存')
  },
  beforeCreate(){
    console.log('实例已经初始化，data中没有数据');
  },
  created(){
    console.log('实例已经初始化，data中的值还没有别渲染');
  },
  beforeMount(){
    console.log('实例挂载到DOM元素前');
  },
  mounted(){
    console.log('实例被挂载到DOM元素上');
  },
  beforeUpdate(){
    console.log('实例更新前');
  },
  updated(){
    console.log('实例已经更新');
  },
  deforeDestroy(){
    console.log('实例销毁之前');
  },
  destroyed(){
    console.log('实例销毁')
  }
```
### 使用钩子函数比较keep-alive存在与不存在的区别
#### keep-alive存在情况下
初次渲染
![](https://user-gold-cdn.xitu.io/2019/4/2/169db49e2dbfc211?w=356&h=161&f=png&s=28264)
再次跳转渲染
![](https://user-gold-cdn.xitu.io/2019/4/2/169db4a6ecce487b?w=371&h=28&f=png&s=3845)
#### keep-alive不存在情况下
初次渲染
![](https://user-gold-cdn.xitu.io/2019/4/2/169db4b3c8e65e6e?w=362&h=142&f=png&s=26127)
再次跳转渲染，首先跳转到主页
![](https://user-gold-cdn.xitu.io/2019/4/2/169db4be0aa8294d?w=377&h=46&f=png&s=4219)
然后跳转回到Test-Keep页
![](https://user-gold-cdn.xitu.io/2019/4/2/169db4c80d3651ca?w=364&h=108&f=png&s=20378)

#### 区别
1. 实例销毁：存在情况下，在路由切换的时候实例不会被销毁；不存在情况下，在路由切换的时候实例会被销毁。
2. 生命周期：存在情况下，在路由重新切换到Test-Keep页的时候，只会执行activated钩子函数(因为实例未被销毁)；不存在情况下，在路由重新切换到Test-Keep页的时候会重新走一遍生命周期。
3. 页面保护：存在情况下，在路由重新切换到Test-Keep页的时候，会保持上次切换停留的地方；不存在情况，会重新渲染。