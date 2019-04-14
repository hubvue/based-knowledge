---
title: React16：render函数的多种返回值
layout: post
subtitle: React16新增render函数的返回值
date: '2019-04-14 08:12:44'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553337506344&di=e4bad495d76574ca77897e2d0e0e6134&imgtype=0&src=http%3A%2F%2Fs1.51cto.com%2Fwyfs02%2FM01%2F88%2F7F%2FwKiom1f55HCSS-DrAACSkyHme8o914.png-wh_651x-s_1436211364.png
tag:
- React
---

在React16之前，类组件的render函数和函数组件的返回值是有限制的，只能返回html和自定义组件,并且如果返回多行Dom的话必须在外层加入一个根元素包裹起来。React16出现之后在render函数返回值上发生了很大的变化，让我们在写React代码过程中更顺滑。

### string
React16问世之后，render函数可以返回一串String，渲染的时候直接渲染到视图中。
```js
render(){
    return "Hello React";
}
```
### null
React16之后如果组件不需要在视图上渲染的时候，可以直接返回一个`null`。
```js
render(){
    return null;
}
```
### array
React16出现之后render函数支持返回值中返回一个数组，在渲染过程中会把数组中的东西，依次渲染出来。这可以作为多行元素输出而不是用外层包裹元素的一种解决方案，但是不怎么好。
```jsx
render(){
    return [
        <li>1</li>,
        <li>2</li>,
        <li>3</li>,
        <li>4</li>
    ]
}
```

### fragments
我认为render支持返回值为`fragments`才是这次React16在返回值上的重头戏。

以前在写多行DOM元素输出的时候会在最外层加一层根元素，这样才不会报错。一个多行DOM添加一个根元素不算什么，那如果一个项目很大，这得多渲染多少个`div`，这要浪费多少渲染资源。可能React开发者也遇到了这个问题，所以才新增了这个返回值---`fragments`。
```jsx
    render(){
        return (
            //这两个标签在渲染的时候不会渲染成DOM元
            <>
                <li>1</li>
                <li>2</li>
                <li>3</li>
                <li>4</li>
            </>
        )
    }
```