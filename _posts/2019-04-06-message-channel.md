---
title: MessageChannel消息频道
layout: post
subtitle: 学习MessageChannel消息频道
date: '2019-04-06 15:01:46'
author: wangchong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554544846984&di=9c35de18c9aeb23910dc9d72c6814d8a&imgtype=0&src=http%3A%2F%2Fnews.mydrivers.com%2Fimg%2F20170414%2F4a584bd038434126bcbdd7942f919d24.jpg
tag:
- JavaScript
---

### MessageChannel
MessageChannel可以实现一个通讯机制，允许我们创建一个新的消息通道。它是一个构造函数。

### 实例化
```js
const channel = new MessageChannel();
console.log(channel);
```
![](https://user-gold-cdn.xitu.io/2019/4/5/169ecb37302f6283?w=575&h=82&f=png&s=12157)
实例化对象上有两个MessagePort对，象并且这两个对象是只读的。这两个对象间可以相同通信。例如：
```js
const channel = new MessageChannel();
const port1 = channel.port1;
const port2 = channel.port2;
//监听信息
port1.onmessage = function(val){
    console.log("收到port2的数据",val.data);
}
port2.onmessage = function(val){
    console.log('收到port1的数据',val.data);
}
//发送信息
port1.postMessage('向port2发送数据');
port2.postMessage('向port1发送数据');
```

![](https://user-gold-cdn.xitu.io/2019/4/5/169ecbb57222c7a0?w=341&h=76&f=png&s=4590)