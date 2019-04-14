---
title: Vue动画效果
layout: post
subtitle: 初识Vue动画效果
date: '2019-03-31 23:22:04'
author: wangchong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

Vue原生有一个`transition`组件，可以用于做动画。把想要做动画的DOM元素放在`transiton`标签内就可以了。Vue原生只是提供了这样一种途径，但是并没有提供动画效果，动画效果还需要我们开发者自己来做。
### 过渡类名
Vue动画使用一系列钩子来得到动画执行时期的状态。

在进入/离开的过渡中，会有 6 个 class 切换:
1. v-enter：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
2. v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
3. v-enter-to: 2.1.8版及以上 定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。
4. v-leave: 定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
5. v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
5. v-leave-to: 2.1.8版及以上 定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。

![](https://user-gold-cdn.xitu.io/2019/3/31/169d32e37b4a5db9?w=1200&h=600&f=png&s=41989)

### 动画
Vue动画效果有三种方式：
1. 自己使用css来书写动画
2. 使用现有的css动画库
3. 使用现有的js动画库

#### 自己使用css来书写动画
transition组件有个name属性，name的值会把动画过渡阶段的v换掉。例如
> name="fade"  即动画class 为 fade-enter

```css
.fade-enter,.fade-leave-to{
    opacity:0;
    transform: translateY(-20px);
    color:red;
}
.fade-enter-active,.fade-leave-active{
    transition: all 1s;
}
```
```html
<template>
    <div>
        <button @click="show= !show">Toggle</button>
        <transition name="fade">
            <p v-if="show"> Hello Vue Transition</p>
        </transition>
    </div>
</template>
<script>
export default {
     data(){
        return{
            show : true,
        }
    },
}
</script>
```
![](https://user-gold-cdn.xitu.io/2019/3/31/169d43cf38e2d502?w=194&h=103&f=png&s=3397)

#### 使用现有的css动画库
使用现有的css库,需要用到Vue提供的自定义过度类名。他们的优先级高于普通的类名。过度类名有一下几个：
1. enter-class
2. enter-active-class
3. enter-to-class (2.1.8+)
4. leave-class
5. leave-active-class
6. leave-to-class (2.1.8+)

例如使用animate.css这个css动画库
> 引入:<link href="https://cdn.bootcss.com/animate.css/3.7.0/animate.min.css" rel="stylesheet">

transition需要写成自定义过渡类名的形式

```html
<transition name="custom-classes-transition" leave-active-class="animated bounce" enter-active-class="animated flash">
    <p v-if="show"> Hello Vue Transition</p>
</transition>
<script>
export default {
     data(){
        return{
            show : true,
        }
    },
}
</script>
```
#### 使用现有的js动画库
Vue动画也可以使用js动画库，当使用js动画库的时候首先要修改一下transition组件,需要添加一个css属性设置为false。通过钩子函数进行阶段的设置。
```html
<transition @before-enter = "beforeEnter" :css="false" @enter = "enter">
    <p v-if="show"> Hello Vue Transition</p>
</transition>
```
Vue提供一下几种钩子函数
```html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>
<script>
export default {
     data(){
        return{
            show : true,
        }
    },
   methods: {
  // --------
  // 进入中
  // --------

  beforeEnter: function (el) {
    // ...
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  enter: function (el, done) {
    // ...
    done()
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },

  // --------
  // 离开时
  // --------

  beforeLeave: function (el) {
    // ...
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
    // ...
  }
}
}
</script>

```