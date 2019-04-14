---
title: Node终端debug
layout: post
subtitle: 使用node终端进行代码调试
date: '2019-02-11 01:11:27'
author: wang chong
catalog: true
header-img: img/bg/hello_world.jpg
tags:
- NodeJS
---

### NodeJS终端调试器
在Node.js中，提供了一个在命令行界面中可以使用的调试器，可以利用该调试器来进行一些应用程序的简单调试，例如显示代码、变量及函数的返回值等。

在Nodejs中，提供了一个通过简单TCP洗衣来访问的调试器。
#### 启动调试
在终端中使用下面命令来启动调试器
> node debug test.js

![](https://user-gold-cdn.xitu.io/2019/2/10/168d7fd5b799cc89?w=658&h=185&f=png&s=72126)
打开之后就是上面这样子。

#### 调试命令
##### continue
如果需要继续执行当前被暂停执行的脚本，可以使用continue命令，在debug后面输入‘cont’命令或者‘c’命令。
![](https://user-gold-cdn.xitu.io/2019/2/10/168d80f325565ca7?w=460&h=78&f=png&s=27531)
##### next
如果不需要执行完剩余的脚本，可以next命令，在debug后面输入‘next’命令或者‘n’命令，就将程序执行到下一句可执行的代码之前。
![](https://user-gold-cdn.xitu.io/2019/2/10/168d811a50d68865?w=319&h=131&f=png&s=16492)
next命令是以一条可执行的命令为单位执行，因此在执行到函数的时候并不会进入函数内部去，而是直接返回函数的执行结果。如果想进入函数内部就需要下面这个命令。
##### step
如果想在next命令执行过程中进入函数内部，就需要step命令。程序将要执行函数调用的代码的时候在debug后面输入‘step’命令或者‘s’命令，程序会停留在函数内的第一行代码。然后再使用next命令逐渐的执行代码。
![](https://user-gold-cdn.xitu.io/2019/2/10/168d81542c41621f?w=658&h=220&f=png&s=64562)
##### out
当使用step命令进入函数内部的时候，可以使用out命令立即执行完函数内剩余的所有代码，也就是说立即执行完函数。暂停在该函数之后的下一行代码之前。在debug后面使用‘out’命令或者‘o’命令。
![](https://user-gold-cdn.xitu.io/2019/2/10/168d81863f0ceab9?w=656&h=121&f=png&s=27651)
上面代码输出了1。test函数执行完毕。

### 观察变量值与表达式执行的结果
在程序调试过程中，可以通过使用命令来观察某个变量的值或某个表达式的执行结果。
#### watch('观察时使用的表达式')
在watch命令中，可以书写任何需要观察的表达式，命令行窗口中将在每执行一行代码之后均显示该表达式的执行结果。

修改一下js代码。
```javascript
function test(){
    var n = 1;
    for(var i = 0; i < 5; i ++){
        n ++;
    }
}
test();
```
启动调试，根据上面操作进入到test函数内部。执行以下观察。
> watch('n==4')

![](https://user-gold-cdn.xitu.io/2019/2/11/168d840078001f5b?w=631&h=48&f=png&s=14298)
什么也没有出现，第一次next命令
![](https://user-gold-cdn.xitu.io/2019/2/11/168d840778f0f473?w=610&h=172&f=png&s=37019)
可以看到 
> n==4 false

继续next，当执行n++第三次的时候。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d841d2f452491?w=541&h=173&f=png&s=27877)
可以看到
> n==4 true

#### watchers
可以使用watchers命令查看所有观察表达式的运行结果或变量的变量值。
> watchers
![](https://user-gold-cdn.xitu.io/2019/2/11/168d842f84fa31c0?w=475&h=61&f=png&s=18242)

#### unwatch('观察时使用的表达式') 
使用unwatch方法解除指定的观察对象。
> unwatch('n==4');

解除之后使用watchers查看观察表达式。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d8450c3ce6e04?w=321&h=70&f=png&s=10043)
已经没有了。

### 断点
当我们在调试程序的时候执行结果不是我们所预期的时候，要通过next命令一句一句的debug，代码少的还好，如果代码很重，那么这样简直是噩梦式的存在。在NodeJS中的调试中也可以像在编辑器中那么打断点。指定调试过程中所要停止的位置。
#### 设置断点
需要设置断点的时候使用setBreakpoint命令或者sb命令。方法如下：
```javascript
setBreakpoint(filename,line)
sb(filename,line)
```
1. 第一个参数用于指定需要设置断点的脚本文件名
2. 第二个参数用于指定将断点设置在第几行
3. 如果省略第一个参数，代表在当前正在运行的脚本文件中设置断点，当两个参数值均省略时，将把断点设置在调试器中下一句执行的脚本代码处。

例如：在本例中的第4行打断点
> sb(4)

![](https://user-gold-cdn.xitu.io/2019/2/11/168d851b30f1df9c?w=653&h=163&f=png&s=47668)
设置断点后使用c命令，脚本代码将一直运行到断点处停止，
![](https://user-gold-cdn.xitu.io/2019/2/11/168d852bddcba509?w=482&h=137&f=png&s=19819)

#### 清除断点
在使用了setBreakpoint命令设置断点之后，可以使用clearBreakpoint命令或cb命令取消断点，方法如下所示:
```javascript
clearBreakpoint(filename, line)  
cb(filename, line) 
```
1. 第一个参数用于指定需要取消断点的脚本文件名
2. 第二个参数用于指定取消第几行位置处的断点
3. 如果省略第一个参数，代表取消当前正在运行的脚本文件中的断点，当两个参数值均省略时，将取消调试器中下一句执行的脚本代码处的断点。

### 调试器中的其他命令
除了上述调试代码的命令之外，还有其他的实用的命令。
#### backtrace命令
当代码中有深层函数调用的时候，可以实用backtrace命令或者bt命令查看该函数及其外层各函数的返回位置，包括返回代码的行号及起始字符所在位置。

修改一下代码：
```javascript
var i=0;  
var func1=function(){  
var func2=function(){  
var func3=function(){  
var func4=function(){  
var func5=function(){  
                    i=i+1;  
                }  
func5();  
            }  
func4();  
        }  
func3();  
    }   
func2();  
}  
func1();  
console.log(i); 
```
通过next和step命令进入最里层函数内部，执行bt命令，得到以下函数调用栈
![](https://user-gold-cdn.xitu.io/2019/2/11/168d859288dbe479?w=406&h=253&f=png&s=31340)

#### list命令
在调试器中调试代码的过程中，可以使用list命令查看当前所要执行代码之前及之后的几行代码。

> list(n)

在list命令中，可使用一个整数型参数，用于指定查看当前所要执行代码之前及之后的多少行代码。例如：list(3)
![](https://user-gold-cdn.xitu.io/2019/2/11/168d85aabf0e7a1d?w=350&h=139&f=png&s=16709)

#### repl命令
在调试过程中可以使用repl进入REPL运行环境
![](https://user-gold-cdn.xitu.io/2019/2/11/168d85b7c6746d96?w=548&h=52&f=png&s=17473)

#### restart命令
在调试过程中随时可使用restart命令重新开始脚本的调试。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d85c3391432b7?w=657&h=186&f=png&s=70919)

#### kill命令
在调试过程中随时可使用kill命令终止脚本文件的调试。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d85ce1a98122a?w=656&h=57&f=png&s=24000)

#### run命令
在使用了kill命令终止脚本文件的调试后，可使用run命令重新开始脚本文件的调试。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d85d849cbf88d?w=649&h=206&f=png&s=65872)

#### scripts命令
在加载了一些模块文件后，可使用scripts命令查看当前正在运行的文件及所有被加载的模块文件名称（不包含Node.js中的内置模块）。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d8604085ddb05?w=421&h=45&f=png&s=7567)

#### version命令
version命令用于显示Node.js所用V8 JavaScript引擎的版本号。
![](https://user-gold-cdn.xitu.io/2019/2/11/168d860cadf0f2bd?w=444&h=54&f=png&s=8655)

### node调试工具
除了上面的这些原生的方式，也可以通过node调试工具来调试。
[node-inspector调试工具](https://www.npmjs.com/package/node-inspector)