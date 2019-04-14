---
title: Vue初探-计算属性与watch监听
layout: post
subtitle: Vue计算属性及watch监听相关知识整理
date: '2019-03-31 15:30:00'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

### 计算属性
在Vue构造传的对象中，data表示数据，当我们在视图中如果想要数据进行操作，直接在双大括号里写是很麻烦的，所以就有了计算属性。计算属性也是和data相同是Vue构造时传入对象的一个属性，它是一个对象`computed`。

计算属性会分析计算属性中的方法，当外部改变的值与计算属性中的函数的值有依赖关系的话，会执行；没有依赖关系就就不会执行。

计算属性执行完之后会产生一个缓存，当下次再执行这个计算属性的时候，直接走缓存，不再执行代码。
#### 计算属性的方法
上面知道计算属性`computed`是一个对象，它的值有两种形式：一种是函数，一种是对象。
##### 函数
当`computed`的属性是函数的时候，必须要有返回值，因为vue底层会时刻监听computed中的方法，不能是异步的。

使用的时候就在双大括号中写上函数名就可以。例如：
```html
<div id="app">
    <div> {{ fullName }}</div>
</div>
<script type="text/javascript">
    new Vue({
        el : "#app",
        data : {
            num : 0,
            firstName : "chong",
            lastName : "wang",
        },
        computed : {
            fullName(){
                 alert("我执行了")
                 return this.lastName + " " + this.firstName;
            }
        },
    })
</script>
```
##### 对象
如果`computed`的属性是一个对象，那么这个对象有两个方法：一个是get，一个是set。

如果想让计算属性去依赖其他属性就可以使用get方法，get方法必须要有返回值；如果想让计算属性改变从而影响其他数据的改变就使用set的方式。

```html
<div id="app">
    <div> {{ fullName }}</div>
    <button v-on:click = "changeName">click me</button>
    <button v-on:click = "changeFullName">changeFull</button>
</div>
<script type="text/javascript">
    new Vue({
        el : "#app",
        data : {
            num : 0,
            firstName : "chong",
            lastName : "wang",
        },
        computed : {
            fullName : {
                //当获取fullName值的时候走get函数
                get(){
                    alert("我执行了get")
                    return this.lastName + " " + this.firstName;
                },
                //当设置fullName值的时候走set函数
                set(newValue){
                    var arr = newValue.split(" ");
                    this.lastName = arr[0];
                    this.fitstName = arr[1];
                }
            }
        },
        methods : {
            changeName(){
                this.firstName = "huimin";
                this.lastName = "zhou";
            },
            changeFullName(){
                this.fullName = "xiao hong";
            }
        }
    })
</script>
```
### watch监听
watch顾名思义就是监听，用于监听数据的变化。watch中的方法属性设置为所依赖的属性值(说白了，watch中的方法的名字和所要监听的data中的属性名相同)，方法有一个形参，为所监听属性的值。

watch中的方法是没有返回值的，并且watch中的方法可以是异步的。
```html
<div id="app">
    <input type="text" v-model = "seachInfo">
    <div v-if = "showSearching">正在搜索...</div>
    <ul>
        <li v-for = "item in result"> {{ item }}</li>
    </ul>
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            seachInfo : "",
            showSearching : false,
            result : [],
        },
        watch : {
            seachInfo(query){
                console.log(query);
                this.showSearching = true;
                setTimeout(() => {
                    this.result = ['html','css','js'];
                    this.showSearching = false;
                },500)
            }
        }
    })
</script>
```