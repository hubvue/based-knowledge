---
title: React 生命周期
layout: post
subtitle: 学习React生命周期
date: '2019-03-24 11:58:51'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

> 每个软件都有从开始创建到运行到最终销毁的一段路程，这可以说成是软件的生命周期。

### React生命周期
React的核心是基于组件的，React的组件也是有声明周期的，和大部分软件相同，它同样具有从开始创建到运行再到最终销毁的这段路程。

> React组件的生命周期分为三个阶段：挂载 -> 更新 -> 卸载。

每个阶段有与之相对应的钩子函数。

![](https://user-gold-cdn.xitu.io/2019/3/23/169ab4171c2bacd7?w=488&h=636&f=png&s=148761)
上图是官网的一张生命周期的图。下面来分析一下每个阶段。

#### 挂载阶段
挂载阶段有4个钩子函数和1个静态属性分别是：
1. defaultProps={} ：初始化默认属性。
2. constructor() ：调用父类的构造函数，初始化状态。
3. componentWillMount() ：组件将要被挂载，将要被渲染。
4. render() ：渲染组件。
5. componentDidMount() ：组件已经被挂载

##### static defaultProps
`defaultProps` 是组件中的一个属性，并且使一个定义在构造函数上的静态属性，它的作用是定义组件的默认属性并且配合同样定义在构造函数上的`propTypes`使用，`propTypes`的作用是限制`defaultProps`中默认属性的类型。
```jsx
export default class Hello extends React.Component{
    static defaultProps = {     //初始化默认属性
        name : "wang",
    }
    static propTypes = {
        name : PropTypes.string      //记得导入类型包  
    }
}
```
##### constructor
`constructor`是类的构造函数，同样也是React生命周期的一个钩子函数。在这个阶段可以调用父类的构造函数也可以初始化默认状态。
```jsx
export default class Hello extends React.Component {
    constructor(props){
        super(props)    //调用父类的构造函数把构造函数的参数传进去。
        this.state = {
            isLoading : false,
        }
    }
}
```
##### componentWillMount
`componentWillMount`钩子函数在组件将要被挂载，将要被渲染的时候触发，并且在这个阶段可以修改状态。
```jsx
export default class Hello extends React.Componet {
    constructor(props){
        super(props);
        this.state = {
            isLoading : false,
        }
    }
    componentWillMount(){
        this.setState({
            isLoading : true,
        })
    }
}
```
##### render
`render`表示组件开始渲染，在render中return出组件的JSX语法，JSX语法只能有一个根标签，并且在render阶段只能使用props和state，不允许修改状态和DOM输出。
```jsx
export default class Hello extends React.Component {
    static defaultProps = {
        name : wang,
    }
    constructor(props){
        super(props);
        this.state = {
            title : "Hello",
        }
    }
    render(){
        return (
            <div>
                {this.state.title + " " + this.props.name}
            <div>
        )
    }
}
```
##### componentDidMount
`componentDidMount`阶段表示组件渲染完成。在这个阶段可以得到真正的DOM元素(可以使用jQuery得到)，并且可以修改DOM(可以使用ReactDOM.findDOMNode(this.refs.prop))。
```jsx
export default class Hello extends React.Component{
    render(){
        return (
            < input
                ref = "input"
            />
        )
    }
    componentDidMount(){
        console.log(ReactDOM.findDOMNode(this.refs.input)   //获取到input元素。
    }
}
```
#### 更新阶段
更新阶段是指当组件中的状态改变，或者是父组件改变子组件的属性值。更新阶段有5个钩子函数如下：
1. componentWillReceiveProps ：父组件修改子组件属性。
2. shouldComponentUpdate ：判断节点是否要重新去render，返回true和false，必须要有返回值。
3. componentWillUpdate ：组件将要被更新。
4. render ： 渲染页面。
5. componentDidMount ：组件更新完成。

##### componentWillReceiveProps
`componentWillReceiveProps`钩子函数表示当父组件改变子组件`props`的时候，这个钩子函数触发。

钩子函数接收一个参数，表示更新后的属性
例如：

```jsx
//子组件List
export default class extends React.Component{
    render(){
        let list = this.props.names.map((value,key) => <li key={key}>{value} </li> );
        return (
            <ul>{list} </ul>
        )
    }
    componetWillReceiveProps(nextProps){
        console.log(nextProps) ;  //  ['hahaha','heiheihei','xixixi']
    }
}
```
```jsx
//父组件App
import List from "./List";

export default class App extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            names : ['zhangsan','lisi','wanger'],
        }
        this.handleChange = this.handeChange.bind(this);
    }
    handleChange(){
        this.setState({
            names : ['hahaha','heiheihei','xixixi']
        })
    }
    render(){
        return (
            <div 
                onClick = {this.handleChange}
            >
                <List name = {this.state.names}> </List>
            </div>
        )
    }
}
```
定义上面父子组件当我们点击div的时候事件触发，改变父组件状态，更改子组件names属性，从而引起子组件更新。

##### shouldComponentUpdate
无论是父组件改变子组件属性发生子组件更新，还是子组件自己状态改变发生更新，都要经过`shouldComponentUpdate`钩子函数。

`shouldComponentUpdate`钩子函数用于判断节点是否要重新去渲染,返回true或者false：返回true就表示要渲染，返回false就不渲染，因此我们可以自己判断组件是否要更新,用于性能优化。

`shouldComponentUpdate`钩子函数同样接收一个参数，表示更新后的属性值。
```jsx
export dafault class List extends React.Component {
    
    shouldComponentUpdate(nextProps){
        console.log(nextProps);
        return true;  //return true更新  false 不更新
    }
}
```
##### componentWillUpdate
`componentWillUpdate`表示组件将要被修改，这个阶段不能修改属性和状态。

`componentWillUpdate`钩子函数接收一个参数，表示更新后的属性值。
```jsx
export default class List extends React.Componnet{
    componentWillUpdate(nextProps){
        console.log(nextProps);
    }
}
```

##### render 
`render`钩子函数用于渲染页面，和挂载阶段的render相同。
```jsx
export default class List extends React.Component {
    render(){
        return (
            <div>
                Hello React
            </div>
        )
    }
}
```
##### componentDidUpdate
`componentDidUpdate`钩子函数表示组件更新完成，并且可以修改DOM。

`componentDidUpdate`接收一个参数，这个参数与之前的不同，它接收的参数表示修改之前的属性。
```jsx
export dafault class List extends React.Component {
    componentDidUpdate(oldProps){
        console.log(oldProps);
    }
}
```
#### 卸载阶段
卸载阶段表示组件被删除或者销毁所做的事情，一般我们会在这个阶段进行清理操作，比如说删除计时器和事件监听器等等。

卸载阶段只有一个钩子函数。
1. componentWillUnmount ：在删除组件之前进行清理操作。

##### componentWillUnmount
`componentWillUnmount`这个钩子函数默认是不会触发的，可以使用ReactDOM.unmountComponentAtNode(elem) 找到指定的DOM元素，移出掉。

> 需要注意的是：如果要销毁在使用ReactDOM.unmountComponentAtNode(elem) elemb必须是组件的根元素。

```jsx
export dafalut class App extends React.Componnet {
    constructor(props){
        super(props);
        this.handleChange = this.handleChange.bind(this);
    }
    handleChange(){
        ReactDoM.unmountComponentAtNode(document.getElementById('app'));
    }
    render(){
        return (
            <div
                onClick = {this.handleChange}
            >
             Hello React   
            </div>
        )
    }
    componentWillUnmount(){
        console.log("组件将要销毁");
    }
}
ReactDom.render(
    <App />
    document.getElementById("app")
)
```
### 总结
总结一下几个重要的钩子函数。
1. componentWillUnmount ：在组件卸载的时候做一些清理操作。
2. componentDidMount ：这个阶段可以获取到真是的DoM节点。
3. componentWillReceiverProps ： 通过父组件控制子组件。
4. shouldComponentUpdate ： 动态设置更新增强性能。
5. render ：控制state