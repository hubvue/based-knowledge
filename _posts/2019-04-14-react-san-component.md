---
title: React三种常用组件
layout: post
subtitle: React中三种常用的组件
date: '2019-04-14 10:56:59'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

React核心就是组件，基本上是处处即组件。

## 类组件
类组件是最基本的组件，同时也是写的最多的组件。
```jsx
import React ,{Component} from "react";

class Greeting extends Component{
    constructor(props){
        super(props);
        this.state = {count: props.initialCount};
    }
    //默认props
    static defineProps = {
        name: "普通的Component组件"
    }
    render(){
        // return  "Hello World";
        return [
            <li key="A">hello</li>,
            <li key="B">world</li>,
            <li key="C">Hello World</li>,
            "Hello World"
        ]
    }
}
export default Greeting;
```
对于一个平常的类组件是非常的复杂的，类组件又有state又有props，而且拥有一个完整的生命周期。类组件特别庞大。所以就可以采用函数式组件。
## 函数式组件
函数式组件就是一个简单的函数，因为在React16.8之前只能接收`props`，函数式组件也可以称为无状态组件，非常的简单。

当一个组件只是一个单纯的显示信息(例如：列表页)，不需要state，并且也不需要完整的生命周期，那么就可以使用函数式组件来完成。

函数式组件，不需要关心组件的一生命周期和渲染过程中的钩子函数，更简介。

函数式组件没有自身state，相同的props输入必然会得到相同的组件展示，也就是说函数式组件是纯函数，即函数式组件也可以成为`纯函数组件`。
```jsx
import React, {Component} from "react";

const Button = ({day}) => {
    return (
        <>
            <button
                onClick={()=> console.log(day)}
            >我是{day}</button>
        </>
    )
}
class Greeting extends Component{
    render(){
        return <Button 
                day="函数式组件"
                
                />
    }
}
export default Greeting;
```

## 纯组件
React有一个很大的毛病，比如说有一个列表组件，当列表中只有一个li改变，React渲染会把所有的li全部都会渲染一遍，把所有的比较，渲染中的性能优化全部交给domdiff来做。项目很小的话，没有很多区别；如果项目越来越大，性能就会慢很多。

上面这个毛病是可以改变的，React类组件中有一个生命周期`shouldComponentUpdate`，它必须返回一个boolean值，当返回false的时候直接回到原来的状态不进行渲染，当返回true的时候才会执行render函数渲染。因此我们可以手动的判断`state和props`改变前和改变后的值是否发生变化，来判断是否进行渲染。

但是每次在开发过程中手写`shouldComponentUpdate`来判断是否渲染太过麻烦，于是可以借助`react-addone-pure-render-mixin`这个插件重写了shouldComponentUpdate，不需要我们自己写方法,但是这个也只是浅比较，如果states中有对象的话，则不会发生作用。

后来就有了PureComponent这个组件，这个组件在React内部继承自Component，并且有了`react-addone-pure-render-mixin`的特性。PureComponent和Component的功能基本上一致，不同的是在性能调优上起到作用。
```jsx
class Gretting extends PureComponent {
    constructor(props){
        super(props);
        this.state = {
            todos : todos,
            temps : temps
        }
    }
    clickHandle(){
        let todos = this.state.todos.push("zhangsan");
        this.setState(()=>({ 
            todos: todos,
        }))
        let temp1 = this.state.temps;
        let temp2 = temp1.push({message: "helloWorld"});
    }
    render(){
        return (
            <>
                <button onClick={()=>{this.clickHandle()}}>点击</button>
                <ul>
                    {this.state.todos.map((todo,index) => <Todo key={index} value={todo}/>)}
                </ul>
            </>
        )
    }
}

export default Gretting;
```
### 什么情况下使用纯组件
PureComponent相比于Component一直需要在shoulComponentUpdate中计算是否需要渲染，当子组件中的数据不固定的时候，子组件不停的渲染，那么可以使用PureComponent。如果组件的数据基本的固定，那么就不需要使用PureComponent了，数据固定使用PureComponent反而会增加了计算的负担。
### 关于浅比较
上面提到了浅比较，浅比较的意思是只比较一层，也就是说只比较`this.state`的直接属性，如果其中还有对象或者数组的话，内部的值是不会进行比较的。

我们都知道javascript值类型分为两种：引用值和原始值。

原始值有：
- String
- Number
- Undefined
- Null
- Boolean
- Symbol

引用值有：
- Object(object、array、function)

javascript内存分为栈区和堆区：原始值存放在栈区，引用值存放在堆区。
#### 栈区
原始值在栈区是直接把值存放在栈区中。
```js
var a = 1;
var b = 2;
var c = 3
```


![](https://user-gold-cdn.xitu.io/2019/4/14/16a19719ef6e7ce1?w=305&h=251&f=png&s=6343)

当原始值做赋值操作的时候，直接把栈区中的值赋予了一个新的变量。
```js
var d = a;
```
![](https://user-gold-cdn.xitu.io/2019/4/14/16a1972dd3a3b577?w=288&h=300&f=png&s=7103)
因此在PureComponent中可以直接比较出两个修改前后的值是否一致。
#### 堆区
引用值是把对象存放在堆区，而变量的值是引用值在堆区的地址。比如说
```js
var obj = {name:'wang'}
```

![](https://user-gold-cdn.xitu.io/2019/4/14/16a197815efc425c?w=603&h=308&f=png&s=18573)

当把一个对象或一个数组赋予一个新的变量的时候，堆区是不发生改变的，只是在栈区把当前对象的地址赋予了一个新的变量。
```js
var temp = obj;
```
![](https://user-gold-cdn.xitu.io/2019/4/14/16a19790049681a5?w=585&h=367&f=png&s=24725)
因此现在我们知道，无论如何改变对象中的值，obj和temp总是相等。

![](https://user-gold-cdn.xitu.io/2019/4/14/16a197b35d57b8a2?w=400&h=173&f=png&s=12955)
这种形式PureComponent中就无法进行，无论怎么改变，改变前和改变后都是相同的。因此我们想如果改变后是一个新的对象不就可以了吗？