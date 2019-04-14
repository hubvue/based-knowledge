---
title: Vue-Directive
layout: post
subtitle: Vue自定义指令
date: '2019-03-31 23:23:17'
author: wangchong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

Vue有着它的很强大的指令系统。回顾一下什么是指令：例如`v-on`,v代表vue，on表示指令名。

Vue是提供自定义指令的，使用Vue.directive方法就可以自定义属性。

Vue.directive方法有两个参数：
1. 第一个参数是指令的名字，例如：`on`。
2. 第二个参数是一个对象，对象的属性是指令的相关钩子函数，
3. 其他情况下，第二个参数也可以写成一个函数，函数参数和钩子函数参数相同，这种写法可以说成钩子函数的简写形式。

### 指令相关钩子函数
```js
Vue.directive('name',{
    bind(el,binding,vnode,oldVnode){
    //做绑定的准备工作，
    //比如添加事件监听器，或是其他只需要执行一次的复杂操作。
        //只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
    
    },
    inserted(el,binding,vnode,oldVnode){
        //被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于document中）
    },
    update(el,binding,vnode,oldVnode){
        //根据获得的新值执行对应的更新
        //对于初始值也会调用一次
    },
    componentUpdated(el,binding,vnode,oldVnode){
        //被绑定元素所在模板完成一次更新周期时调用
    },
    unbind(el,binding,vnode,oldVnode){
        //做清理操作
        //比如移除bind时绑定的事件监听器等。
    }
})
```
每个钩子会相应传递四个参数。
- el：指令所绑定的元素，可以用来直接操作 DOM 。
- binding：一个对象，包含以下属性：

    - name：指令名，不包括 v- 前缀。
    - value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
    - oldValue：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
    - expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
    - arg：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
    - modifiers：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
- vnode：Vue 编译生成的虚拟节点。移步 VNode API 来了解更多详情。
- oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

### 实现一个简单的指令
使用Vue.directive方法定义指令
```js
Vue.directive('focus',{
   inserted(el){
       el.focus();
   }
})
```
上面定义了一个简单的`focus`指令，作用是当DOM元素被插入到父节点的时候自动聚焦。

使用方式如下：
```html
<input type="text" v-focus>
```
> 还有一点需要注意的是 ：自定义指令如果很长可以写成-连接的形式，但是在Vue.directive()参数中要写成小驼峰形式。