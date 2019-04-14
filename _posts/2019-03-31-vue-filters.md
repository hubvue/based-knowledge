---
title: Vue初探-filter过滤器
layout: post
subtitle: Vue filter过滤器相关知识
date: '2019-03-31 15:30:00'
author: wang chong
catalog: true
header-img: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1554026785443&di=02cb27b4261a056c17ecca6c27e1c433&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F1c7a3a847672e9bc5cc2605b9a39938b.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100
tag:
- Vue
---

### filter过滤器
filter相当于一种规则，我们定义一种filter规则，在使用双大括号写表达式的时候，把规则放在表达式后面，使用`|`隔开。

filter过滤器中写的是方法，方法有两个参数：
1. 第一个参数：value值是表达式的值。
2. 第二个参数：是一个boolean值，可以在双大括号中传入。

filters可以同时对一个表达式定义多个规则，按照顺序执行。
```html
<div id="app">
    {{ msg | upperCase(true) }}
</div>
<script>
    new Vue({
        el : "#app",
        data : {
            msg : "hello world"
        },
        filters : {
            upperCase(value,isFirstWord){
                value = value.toString();
                if(isFirstWord) {
                    return value.charAt(0).toUpperCase() + value.slice(1);
                } else {
                    return value.toUpperCase();
                }
            }
        }
    })
</script>
```