---
title: React props and state
layout: post
subtitle: React中属性和状态学习及二者之间的比较
date: '2019-03-23 18:35:09'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

### Props
Props(properties)：属性，可以说组件是React的核心了，如果把组件比喻成一个管道，那么props就相当于输入。

props可以定义在注册组件的地方，也可以在组件内部定义默认属性，无论在哪里定义，props都是只读的。

props可以应用于JSX中html的元素上，自定义组件的元素上(相当于给子组件传值)，也可以应用于值。

#### 定义默认props
ES5和ES6定义默认的props是不相同的。
#####　ES5
```js
var Hello = React.createClass({
    getDefaultProps : function(){   //设置默认属性
         return { title : '133'};
    }
    propTypes : { //属性校验器，表示必须是string
        title : React.PropTypes.string,
    }  
}) 
```
上面使用`getDefauktProps`定义属性，`propTypes`用于属性的类型检查。

##### ES6
ES6同样有两种方法，由于ES6是使用class类来定义组件的，因此，这两种方法必须是静态。
```js
export default class Hello extends React.Component{
    static defaultProps ={
        title : "Hello React",
    }
    static propTypes = {
        title : React.PropTypes.string,
    }
}
```
运行上面代码会发现报错。报错原因在`React.PropTypes.string`，这是因为在React15.5之前类型检查是集成在React里面的，React15.5之后被抽离了出来。所以需要下载`prop-types`包来解决这个问题。
> npm install prop-types --save

再修改一下代码。
```js
import propTypes from "prop-types";
export default class Hello extends React.Component{
    static defaultProps ={
        title : "Hello React",
    }
    static propTypes = {
        title :propTypes.string,
    }
}
```
这时候运行就不会报错了。

#### 组件属性传值
##### 通过组件属性进行传值
组件属性传值是最常用的一种方式
```html
<Hello name= "" />
```
此时`name`就是`Hello`的属性。name的值类型可以有如下：
> "text"/{123}/{"string"}/{[1,2,3]}/{variable}/js函数表达式

##### setProps({123})
在组件render完之后，通过setProps()也可以把属性传进来。不怎么常用(emmmm...没用过)

##### 通过扩展运算符传值
这种方式可以归纳到第一种，在父组件建立一个对象，通过扩展运算符传值。
```jsx
var props = {
    one : "123",
    two : 321,
}
ReactDOM.render(
    <List {...props} /> ,
    document.getElementById('app')
)
```
这种方式经常用于列表渲染。在子组件中获取
```jsx
export default class List extends React.Component{
    render(){
        let list = this.props.map((value，key) => <li key = {key}> {value} </li>)
        return (
            <ul> {list }</ul>
        )
    }
}
```

### state
state在React中是状态，是组件自身所拥有的东西，并且可以自己设置和改变。

在开发过程中的状态都是我们自己来维护的，比如说发一个请求，请求发送成功会怎么样、失败会怎么样等等。

React是基于状态的，就是在代码中定义了状态，只要在任何地方改变了状态，最初定义状态的地方就会发生改变。

#### setState
React如果想改变一个状态，那么必须通过setState切换撞他，每一次setState之后，Reader就会重新渲染执行一次render，就会触发diff算法进行计算，通过计算生出新的Virtual Dom和现在的Virtual DOM进行比较，发生变化之后执行一次更新。

#### 使用state
state在ES5和ES6上都是不同的。
##### ES5
通过`getInitialState`方法来初始化状态。
```js
var Hello = React.crateClass({
    getInitialState : function(){
        return {
            isloading : false,
        }
    }
})
```
##### ES6
ES6中使用class来定义组件，规定state要注册到`constructor`中。
```js
export default class Hello extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            isloading : false,
        }
    }
}
```
上面两种方式同样是通过`this.setState()`改变状态。

以ES6为例
```jsx
export default class Hello extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            isloading : false,
        }
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick(e){
        this.setState({
            isloading : !this.state.isloading,
        })
    }
    render(){
        return (
            <div
                onClick = {this.handleClick}
            >
                {this.state.isloading ? <h1> Hello</h1> : <h2> World </h2>} 
            </div>
        )
    }
}
```
### Props 和State的比较
#### 相同点
1. 都是纯的JS对象，都包含这一些信息。
2. 都会触发render更新；属性是开始渲染一次性触发render，状态是每次状态改变都会触发render
3. 都具有确定性，渲染前初始化完成。

#### 区别
属性只传递一次，状态是不停的在更新。组件在运行时需要修改的就是状态，属性在组件内运行时是修改不了的。

#### 比较
 
|   |  Props  |  State  |
| --- | --- | --- |
| 能否从父组件获取初始值？   |    能   | 不能   | 
| 能否由父组件修改？  | 能 | 不能 | 
| 能否在组件内部设置默认值？ | 能 | 能 | 
| 能否在组件内部修改？  | 不能 | 能 |
 | 能否设置子组件的初始值？ | 能 | 不能 |
 | 能否修改子组件的值？  ？  | 能 | 不能 |