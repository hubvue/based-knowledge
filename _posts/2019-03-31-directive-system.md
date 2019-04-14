---
title: Vue初探-指令系统
layout: post
subtitle: Vue指令系统相关知识点及例子
date: '2019-03-31 15:15:02'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

### Vue指令系统
Vue含有强大的指令系统，Vue指令就是告诉这段DOM元素要做什么，通过指令可以为DOM元素绑定属性值及绑定事件等等。
**指令语法**
> v-指令名字 + : + 指令的参数 = 指令的表达式

例如：为url标签添加href属性跳转到百度
```html
<div id="app">
    //v-bind指令是用来为DOM元素绑定属性。
    <a v-bind:href = 'url'>百度</a>
</div>
<script>
    const vm = new Vue({
        el : "#app",
        data : {
            url : 'www.baidu.com';
        }
    })
</script>
```
#### 绑定事件
绑定事件使用`v-on`指令。这个指令有一种简写形式，例如绑定click事件：`v-on:click` 等同于`@click`。

绑定事件有一下注意点：
1. 当事件后面表达式是一个函数的时候，事件对象会自动的注入到函数的第一个形参。
2. 当事件后面的表达式是一个函数执行的时候，这种写法可以自主的向函数中传递参数，但是事件对象不会自动的注入到函数的第一个形参，如果需要事件对象，传递一个为$event的实参。

```html
<div id="app">
    <button @click="clickHandle">点击1 </button>    //表达为函数
    <button @click="clickHandle($event)">点击2 </button>  //表达式为函数执行
</div>
<script>
    const vm = new Vue({
        el : "#app",
        data : {
            url : 'www.baidu.com';
        },
        //methods中定义方法
        methods : {
            clickHandle(e){
                console.log(e);
            }
        }
    })
</script>
```
##### 绑定事件的修饰符
在Vue中可以为绑定事件添加修饰符。
1. stop修饰符：在事件类型后面添加stop修饰符，可以阻止事件冒泡。
2. prevent修饰符：在事件类型后面添加prevent修饰符，可以阻止默认事件的发生。

```html
<div id="app">
    <div @click='warpHandle'>
        <div @click.stop="boxHandle">hello Vue</div>    //阻止冒泡
    </div>
    <form   @submit.prevent='submitHandle'> //这里会阻止表单提交的默认行为，便于自己来控制
        <input type="submit" value="提交"/>
    </form>
</div>
<script>
    const vm = new Vue({
        el : "#app",
        data : {
            url : 'www.baidu.com';
        },
        //methods中定义方法
        methods : {
            warpHandle(e){
                console.log('wrapHandle');
            },
            boxHandle(e){
                console.log('boxHandle')
            },
            submitHandle(e){
                console.log('表单提交')
            }
        }
    })
</script>
```
##### 键盘事件的按键修饰符
在键盘事件后面添加[.什么键(同样也可以写键的数值)],当按到此键的时候就会触发事件并且可以链式绑定。

```html
<input type="text" v-on:keyup.enter.space = "keyup">
    <!--  系统按键 必须是系统键+其他键才会触发-->
    <input type="text" v-on:keyup.shift = "shift">
    <button v-on:click.shift = "shift">shift</button>
    <!-- meta键是系统键，根据系统的不同键也是不同的 -->
    <button v-on:click.meta = "shift"> meta</button>
</div>
<script type="text/javascript">
    new Vue({
        el : "#app",
        methods : {
            keyup(event){
                console.log(event);
                    alert("you pressed");
            },
            shift(){
                console.log("shift");
            }
        }
    })
</script>
```

#### 数据的双向绑定
数据的双向绑定指的是：视图的改变可以改变数据，同时数据的改变也可以改变视图。

Vue的数据双向绑定可以通过v-bind和v-on来实现。比如说：input：v-bind把data中的值绑定到input上面的value上面，而v-on使用input事件执行事件函数改变data中的值。
```html
<div id="app">
    {{ value }}
    <input type="text" v-bind:value = "value" v-on:input = "input">
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            value : 30,
        },
        methods : {
            input(event){
                this.value = event.target.value;
            }
        }
    })
</script>
```
在vue中把v-bind和v-on结合产生了v-model来实现数据的双向绑定。下面结果和上面相同
```html
<div id="app">
    {{ value }}
    <input type="text" v-model="value">
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            value : 30,
        },
    })
</script>
```
##### v-model的修饰符
1. trim:这个修饰符在双向绑定的时候input输入空格会忽略
2. number：把输入的东西转为数字,把输入的值parseInt了一下
3. lazy:在input的输入框上，只弹出一次，当输入框失去焦点的时候，才会执行数据的绑定

#### v-html
我们如果想把一个dom元素放到app中就可以使用v-html这个指令，比如说v-html = "html"。但是这个是很危险的，只有特定的不能够让用户操作的才可以用这个，同样为了防止XSS攻击，也需要把这个隐藏起来。

#### v-once
v-once的作用是只执行一次，v-once中的数据只有在初始化的时候渲染一次，其余不会改变v-once中的数据。

```html
<div id="app">
    {{ html }}
    <div v-html = "html"></div>
    <div v-once>
        {{ msg }}
    </div>
    {{ msg }}
    <button v-on:click = "msg = '123'">click me</button>
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            html : `<h1> hello </h1>`,
            msg : "wangchong"
        }
    })
```
#### 条件渲染
##### v-if、v-if-else、v-else
当指令不满足的时候把页面的dom元素delete掉，页面不显示，html代码中也没有v-else-if 和v-else 或找最近的兄弟节点。当v-if为false的时候，才会找v-else-if或者v-else。

使用v-if的时候可以用h5中的template标签来结合使用避免代码冗余。

##### v-shou
当指令后面的值为false的时候的话，会把当前dom节点的display改为none，当指令后面的值为true的时候，就会显示当前节点.行内style样式和css样式的display不会影响v-show。
```html
<div id="app">
    <p v-if = " items > 10"> {{ items }} 个有现货</p>
    <p v-else-if = "items > 1">所剩不多了，快点买吧</p>
    <p v-else>没有库存，请耐心等待</p>
    <template v-if = "items > 50">
        <p >注意事项</p>
        <p >因为剩余很多，现在买打八折！！！</p>
    </template>
    <p v-show = "Ninja" style ="display : none">i am hide</p>
    <p v-show = "! Ninja"> i am here</p>
    <button v-on:click = "Ninja = !Ninja">Ninja </button>
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            items : 50,
            Ninja : true,
        }
    })
</script>
```
#### v-cloak
 当网速很慢的情况加，js代码加载的很慢的时候，使用{{}}数据绑定的时候，当js还没有渲染出来的时候，显示的是{{}}，在我们认为这是乱码的状态。
 
 v-cloak：这个指令的作用是，当vue代码没有渲染出来的时候这个指令作为一个属性存在于dom元素中，当js代码加载完毕之后就会消失，这样的话就可以使用css属性选择器找到这个属性，display:none 当js渲染出来的时候再显示出来。
 
 使用这个可以判断数据是否加载进来从而做页面loading。
 ```html
  <div id="app">
    <div v-cloak >
        {{ message }}
    </div>
</div>
<script type="text/javascript">
    setTimeout(function() {
        new Vue({
            el: "#app",
            data: {
                message: "awngchong",
            }
        })
    }, 5000)
</script>
```
#### 列表渲染
在vue中也是可以使用循环来渲染列表，v-for:类似于`for in`遍历来遍历数组，在v-for中也可以使用其他的数据绑定。

> 使用方式：(别名，索引位) in 表达式

```html
<div id="app">
         <h1>电影列表</h1>
        <ul> 
         <li v-for = "(title,index,arr) in movies">{{ title }} ({{ index }})</li>
        </ul>
    </div>
    <script>
        new Vue({
            el : "#app",
            data : {
                movies : ["妖猫传","芳华","至暗时刻"],
            }
        })
    </script>
```
Vue中的v-for的渲染机制是根据数据的内存地址来渲染的，当内存地址改变的时候，会再次进行渲染，不改变的话不会渲染，当使用click事件点击改变数组中的值的时候，数组的索引没有变是不会渲染的。Vue通过了一种方式，使用Vue.set对数据进行修改。

> Vue.set(对象/数组，属性名/索引位，改变的值)

```html
<div id="app">
    <ul>
        <li v-for = "n in numbers"> {{ n }}</li>
    </ul>
    <button v-on:click = "changeNumber">click me</button>
    <ul>
        <li v-for = "per in person "> {{ per}} </li>
    </ul>
    <button v-on:click = "changeObject">click me</button>
</div>
<script type="text/javascript">
    new Vue({
        el : "#app",
        data : {
            numbers : [1,2,3,4,5],
            person : {
                name : 'wang',
                age : 20,
            }
        },
        methods : {
            changeNumber(){
                Vue.set(this.numbers,1,10);
            },
            changeObject(){
                Vue.set(this.person,"name","chong")
            }
        }
    })
</script>
```
如果数据是数组的话，改变数组中的值数组的地址是不变的，但是可以用push方法在v-for中渲染。是因为Vue底层把数组的push方法进行了重写，变成了可以被监听的方法。

> Vue重写的数组的方法：push、pop、shift、unshift、reverse、sort、splice

##### v-for的运行机制
使用v-for渲染列表能够很好的复用模板，不再进行DOM渲染，从而提高性能，但是它还是有bug的。
当v-for里面有其他dom的时候，v-for认为他们是一样的，就不再对他们进行渲染。如果向让v-for对其他DOM渲染的时候，可以对每一个v-for的DOM指定一个key值，使用`:key=""`绑定，对每个不同的DOM有不同的值就会进行渲染。