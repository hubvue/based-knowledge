---
title: React可控组件与不可控组件
layout: post
subtitle: 学习React的可控组件与不可控组件
date: '2019-03-24 12:38:24'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

### 不可控组件
当一个表单元素设置了`defaultValue`属性的时候，那么这个组件就变成了不可控组件。

为什么这么说呢？

`defaultValue`属性设置的值大多数情况下是不允许更改的，由于React的所有的View是基于状态的改变而动态渲染的，而设置了`defaultValue`是不允许更改，所以就可以称组件为不可控组件。

```jsx
export default class App extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            value : "hello React",
        }
        this.handleChange = this.handleChange.bind(this);
    }
    handleChange(){
        this.setState({
            value : "hello world"
        })
        console.log(this.state.value);
    }
    render(){
        return (
            <input 
                onMouseEnter = {this.handleChange}
                defaultValue = {this.state.value}
            />
        )
    }
}

```

![](https://user-gold-cdn.xitu.io/2019/3/24/169aded87f93853b?w=1370&h=111&f=png&s=18748)
上面代码是：在input元素上设置`defaultValue`并监听`onMouseEnter`事件，当鼠标移入的时候，状态改变。可以从图上看出，状态改变但是input中的值并没有改变。

我们在书写代码的时候无法通过状态去控制组件，这就是不可控组件。

但是不可控组件并不是非不可控，通过`React.findDOMNode(this.refs.input).value`直接取到DOM元素就可以改变。修改一下上面代码。
```jsx
export default class App extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            value : "hello React",
        }
        this.handleChange = this.handleChange.bind(this);
    }
    handleChange(){
        
        this.setState({
            value : "hello world"
        })
        console.log(this.state.value);
        ReactDOM.findDOMNode(this.refs.input).value = this.state.value;
    }
    render(){
        return (
            <input 
                onMouseEnter = {this.handleChange}
                defaultValue = {this.state.value}
                ref = "input"
            />
        )
    }
}
```

![](https://user-gold-cdn.xitu.io/2019/3/24/169adf12704fab0e?w=1365&h=126&f=png&s=18440)
在图上可以发现，input被改掉了。

### 可控组件
当我们在表单元素上不使用`defaultValue`而使用`value`的使用，组件就变成了可控的了。

上面代码修改一下。
```jsx
export default class App extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            value : "hello React",
        }
        this.handleChange = this.handleChange.bind(this);
    }
    handleChange(){
        
        this.setState({
            value : "hello world"
        })
        console.log(this.state.value);
    }
    render(){
        return (
            <input 
                onMouseEnter = {this.handleChange}
                value = {this.state.value}
            />
        )
    }
}
```
![](https://user-gold-cdn.xitu.io/2019/3/24/169adf3a347513f1?w=1355&h=239&f=png&s=39476)
状态改变，值也改变了，我们发现报了个错。 这个错是因为使用vlaue必须配合一个事件来使用，要么用`onChange`要么把值设置成`readOnly`。

把原来的代码`onMouseEnter`改成`onChange`：

```jsx
export default class App extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            value : "hello React",
        }
        this.handleChange = this.handleChange.bind(this);
    }
    handleChange(e){
        this.setState({
            value : e.target.value
        })
        
        console.log(this.state.value);
    }
    render(){
        return (
            <input 
                onChange = {this.handleChange}
                value = {this.state.value}
            />
        )
    }
}
```

![](https://user-gold-cdn.xitu.io/2019/3/24/169adf7004703943?w=1350&h=112&f=png&s=17679)
这样就可以了，通过value值的改变动态的改变状态。

#### 可控组件的好处
1. 符合React的数据流
2. 数据存储在state中，便于使用
3. 便于对数据进行处理