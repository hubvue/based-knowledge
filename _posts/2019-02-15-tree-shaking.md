---
title: Webpack4:Tree-shaking深度解析
layout: post
subtitle: 深度分析webpack4：Tree-shaking的使用及webpack-deep-scope-plugin的原理
date: '2019-02-15 12:13:05'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- webpack
---

### 什么是Tree-shaking
所谓Tree-shaking就是‘摇’的意思，作用是把项目中没必要的模块全部抖掉，用于在不同的模块之间消除无用的代码，可列为性能优化的范畴。

Tree-shaking早期由rollup实现，后来webpack2也实现了Tree-shaking的功能，但是至今还不是很完备。至于为什么不完备，可以看一下[百度外卖的Tree-shaking原理](https://juejin.im/post/5a4dc842518825698e7279a9)

### Tree-shading原理
Tree-shaking的本质用于消除项目一些不必要的代码。早在编译原理中就有提到DCE(dead code eliminnation)，作用是消除不可能执行的代码，它的工作是使用编辑器判断出某些代码是不可能执行的，然后清除。

Tree-shaking同样的也是消除项目中不必要的代码，但是和DCE又有略不相同。可以说是DCE的一种实现，它的主要工作是应用于模块间，在打包过程中抽出有用的部分，用于完成DCE。


Tree-shaking是依赖ES6模块静态分析的，ES6 module的特点如下：
1. 只能作为模块顶层的语句出现
2. import 的模块名只能是字符串常量
3. import binding 是 immutable的

依赖关系确定，与运行时无关，静态分析。正式因为ES6 module的这些特点，才让Tree-shaking更加流行。

主要特点还是依赖于ES6的静态分析，在编译时确定模块。如果是require，在运行时确定模块，那么将无法去分析模块是否可用，只有在编译时分析，才不会影响运行时的状态。

### Webpack4的Tree-shaking
webpack从第2版本就开始支持Tree-shaking的功能，但是至今也并不能实现的那么完美。凡是具有副作用的模块，webpack的Tree-shaking就歇菜了。

#### 副作用
副作用在我们项目中，也同样是频繁的出现。知道函数式编程的朋友都会知道这个名词。所谓模块(这里模块可称为一个函数)具有副作用，就是说这个模块是不纯的。这里可以引入纯函数的概念。
> 对于相同的输入就有相同的输出，不依赖外部环境，也不改变外部环境。

符合上述就可以称为纯函数，不符合就是不纯的，是具有副作用的，是可能对外界造成影响的。

webpack自身的Tree-shaking不能分析副作用的模块。以lodash-es这个模块来举个例子
```javascript
//test.js
import _ from "lodash-es";

const func1 = function(value){
    return _.isArray(value);
}
const func2 = function(value){
    return value=null;
}

export {
    func1,
    func2,
}
//index.js
import {func2} from './test.js'
func2()
```
上述代码在test.js中引入lodash-es,在func1中使用了loadsh，并且这里不符合纯函数的概念，它是具有副作用的。func2是一个纯函数。

在index.js中只引入了func2，并且使用了func2，可见整个代码的执行是和func1是没有任何关系的。我们通过生产环境打包一下试试看(Tree-shaking只在生产环境生效)

![](https://user-gold-cdn.xitu.io/2019/2/15/168eeff584fcb43d?w=654&h=145&f=png&s=47047)
main.js 91.7KB，可见这个结果是符合我们的预期的，因为func1函数的副作用，webpack自身的Tree-shaking并没有检测到这里有没必要的模块。解决办法还是用的，webpack的插件系统是很强大的。

### webpack-deep-scope-plugin
webpack-deep-scope-plugin是一位中国同胞(学生)在Google夏令营，在导师Tobias带领下写的一个webpack插件。(此时慢慢的羡慕)

这个插件主要用于填充webpack自身Tree-shaking的不足，通过作用域分析来消除无用的代码。
#### 插件的原理
这个插件是基于作用域分析的，那么都有什么样的作用域？
```javascript
// module scope start

// Block

{ // <- scope start
} // <- scope end

// Class

class Foo { // <- scope start

} // <- scope end

// If else

if (true) { // <- scope start
   
} /* <- scope end */ else { // <- scope start
  
} // <- scope end

// For

for (;;) { // <- scope start
} // <- scope end

// Catch

try {

} catch (e) { // <- scope start

} // <- scope end

// Function

function() { // <- scope start
} // <- scope end

// Scope

switch() { // <- scope start
} // <- scope end

// module scope end
```
对于ES6模块来说，上面作用域只有function和class是可以被导出的，其他的作用域可以称之为function和class的子作用域并不能被导出实际上归属于父作用域的。

插件通过分析代码的作用域，进而得到作用域与作用域之间的关系。
#### 分析作用域
分析代码的作用域的基础是建立做AST(Abstract Syntax Tree)抽象语法树上面的。这个可以通过[escope](https://github.com/estools/escope)来完成。
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef0eacbb91a10?w=600&h=337&f=gif&s=384877)

拿到解析完的AST抽象语法树，利用图的深度优先遍历找到哪些作用域是可以被使用到的，哪些作用域是不可以被使用到的。从而分析作用域之间的关系和导出变量之间的关系。进而执行模块消除。

#### 插件的不足
JavaScript中还是有一些代码是不会消去的。
##### 根作用域的引用
```javascript
import { isNull } from 'lodash-es';

export function scope(...args) {
  return isNull(...args);
}

```
在根作用域引用到的作用域不会被消除。
#### 给变量重新赋值 
```javascript
import _ from "lodash-es";

var func1
func1 = function(value){
    return _.isArray(value);
}
const func2 = function(value){
    return value=null;
}

export {
    func1,
    func2,
}
```
上述代码中先定义了func1，然后又给func1赋值，这样缺少了数据流分析，同样插件也是不可以的。

#### 纯函数调用
引用作者的例子
```javascript
import _curry1 from './internal/_curry1';
import curryN from './curryN';
import max from './max';
import pluck from './pluck';

var allPass = /*#__PURE__*/_curry1(function allPass(preds) {
  return curryN(reduce(max, 0, pluck('length', preds)), function () {
    var idx = 0;
    var len = preds.length;
    while (idx < len) {
      if (!preds[idx].apply(this, arguments)) {
        return false;
      }
      idx += 1;
    }
    return true;
  });
});
export default allPass;
```
当一个匿名函数被包在一个函数调用中(IIFE也是如此)，那么插件是无法分析的。但是如果加上/\*#_\_PURE__*/注释的话，这个插件会把这个函数调用当作一个独立的域，tree-shaking是可以生效的。

### 探讨的一些问题
我们都知道在这个ES6泛滥的时代，ES6的代码在项目中出现已经很广泛。（先不考虑线上环境打包成ES5）。上面提到插件的利用作用域来分析。能导出的作用域只有class和funciton。function的情况在上面已经说过，现在来探讨一下class的情况。

#### no plugin
当不使用插件的时候，我们来看一下会不会Tree-shaking，预期是会被Tree-shaking。书写下面这样一段简单的代码。
```javascript
class Test{
    init(value){
        console.log('test init');
    }
}
export {
    Test,
}
```
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef2d92f903b42?w=1050&h=190&f=png&s=34459)
我们发现在没有使用插件的情况下，被Tree-shaking了。预期相同。

#### no plugin + 副作用
当我们在不适用插件的情况下，并且引入副作用，观察一下会不会打包，预期是不会打包。书写下面代码。
```javascript
class Test{
    init(value){
        console.log('test init');
        return _.isArray(value);
    }
}
export {
    Test,
}
```

![](https://user-gold-cdn.xitu.io/2019/2/15/168ef31758e44cf2?w=573&h=135&f=png&s=38466)
观察打包结果，main.js 91.7KB，并没有被Tree-shaking，符合预期的结果。
#### plugin + 副作用
当我们使用插件并且代码中存在副作用的情况下，观察打包情况。由于上面的插件原理的铺垫，我们预期这次是可以Tree-shaking的。利用上例代码来测试。
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef360918a3bfd?w=625&h=105&f=png&s=29145)
我们观察可以看出main.js 6.78KB,Tree-shaking生效。

#### plugin + 副作用 + babel
由于用户浏览器对ES6支持度不够的原因，线上的代码不能全是ES6的，有时候我们要把ES6的代码打包成ES5的，放到线上环境来执行。利用上例代码来测试。
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef3bab85a4bc3?w=657&h=116&f=png&s=58155)

？？？ 什么鬼，我没有用到它，为什么这么大？？？  一串懵逼

### 成了babel，败了babel
懵逼懵逼，babel成就了线上生产环境，但失去了Tree-shaking优化。我们来看看怎么回事。
#### 没有副作用的情况
当去除调副作用的时候我们来打包一下。
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef3fa0a1ec0c1?w=801&h=184&f=png&s=26154)
没有找到test init Tree-shaking生效。为什么呢？我们使用babel线上工具编译一下源代码。
```javascript
"use strict";

function _instanceof(left, right) { 
    if (right != null && typeof Symbol !== "undefined" && right[Symbol.hasInstance]) {
         return right[Symbol.hasInstance](left); 
        } else {
             return left instanceof right; 
            } 
        }

function _classCallCheck(instance, Constructor) { 
    if (!_instanceof(instance, Constructor)) { 
        throw new TypeError("Cannot call a class as a function"); 
    } 
}

function _defineProperties(target, props) { 
    for (var i = 0; i < props.length; i++) { 
        var descriptor = props[i]; 
        descriptor.enumerable = descriptor.enumerable || false; 
        descriptor.configurable = true; 
        if ("value" in descriptor) 
            descriptor.writable = true; 
        Object.defineProperty(target, descriptor.key, descriptor); 
    } 
}

function _createClass(Constructor, protoProps, staticProps) { 
    if (protoProps) 
        _defineProperties(Constructor.prototype, protoProps); 
    if (staticProps) _defineProperties(Constructor, staticProps); 
    return Constructor; }

var Test =
    /*#__PURE__*/
    function () {
        function Test() {
            _classCallCheck(this, Test);
        }

        _createClass(Test, [{
            key: "init",
            value: function init(value) {
                console.log("test init")
            }
        }]);

        return Test;
    }();
```
上面可以看到最新的babel和webpack有了契合，在Test立即执行函数的地方使用了 /\*#_\_PURE__*/(忘记可以往上看)，让下面的IIFE变成可分析的，成功了使用了Tree-shaking。

#### 有副作用的情况下
上面探讨情况的时候就得知有副作用的情况下，不可以被打包的。ES6编译代码如下。
```javascript
"use strict";

function _instanceof(left, right) { 
    if (right != null && typeof Symbol !== "undefined" && right[Symbol.hasInstance]) {
         return right[Symbol.hasInstance](left); 
        } else {
             return left instanceof right; 
            } 
        }

function _classCallCheck(instance, Constructor) { 
    if (!_instanceof(instance, Constructor)) { 
        throw new TypeError("Cannot call a class as a function"); 
    } 
}

function _defineProperties(target, props) { 
    for (var i = 0; i < props.length; i++) { 
        var descriptor = props[i]; 
        descriptor.enumerable = descriptor.enumerable || false; 
        descriptor.configurable = true; 
        if ("value" in descriptor) 
            descriptor.writable = true; 
        Object.defineProperty(target, descriptor.key, descriptor); 
    } 
}

function _createClass(Constructor, protoProps, staticProps) { 
    if (protoProps) 
        _defineProperties(Constructor.prototype, protoProps); 
    if (staticProps) _defineProperties(Constructor, staticProps); 
    return Constructor; }

var Test =
    /*#__PURE__*/
    function () {
        function Test() {
            _classCallCheck(this, Test);
        }

        _createClass(Test, [{
            key: "init",
            value: function init(value) {
                console.log("test init")
                return _.isArray(value);
            }
        }]);

        return Test;
    }();
```
这里虽然bable新版契合了webpack，但是还是有一些问题。自己也没有找出是哪里除了问题，作者说JavaScript代码还是有一些是不可以清除的，也许就出现到这里。提供一个作者的插件[Demo](https://diverse.space/webpack-deep-scope-demo/)。

### babel的解决方案
无论是ES6，还是ES5，Tree-shaking不能生效的原因总的归根结底还是因为代码副作用的问题。可想而知代码的书写规范是多么重要。这里我所想出的解决方案有两种。
#### 1.代码的书写规范，尽量避免副作用。
书写代码过程中尽量使用纯函数的方式来写代码，保持书写规范，不让代码有副作用。例如把class类引用的副作用改成纯的。
```javascript
class Test{
    init(value,_){  //参数引入lodash模块
        console.log('test init');
        return _.isArray(value);
    }
}
export{
    Test，
}
```
![](https://user-gold-cdn.xitu.io/2019/2/15/168ef51c818048e1?w=651&h=142&f=png&s=45603)

![](https://user-gold-cdn.xitu.io/2019/2/15/168ef538a650c0bb?w=562&h=198&f=png&s=19899)
可以看出，没有副作用的ES6代码编译成ES5，Tree-shaking也是生效的。
#### 2.灵活使用ES6代码
两套代码。当浏览器支持的时候，就使用ES6的代码，ES5的代码。此方案可参考[浏览器支持ES6的最优解决方案](http://blog.ctomorrow.top/2019/02/05/es6-use/)

### 总结
项目中难免会一些用不到的模块占位置影响我们的项目，Tree-shaking的出现也为开发者在性能优化方面提供了非常大的帮助，灵活使用Tree-shaking才能让Tree-shaking发挥作用，处理好项目中代码的副作用可以使项目更加的完美。


### 引用文章
[webpack 如何通过作用域分析消除无用代码](https://diverse.space/2018/05/better-tree-shaking-with-scope-analysis)

[Tree-Shaking性能优化实践 - 原理篇](https://juejin.im/post/5a4dc842518825698e7279a9)