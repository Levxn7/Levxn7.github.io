---
title: vue源码复习(数据响应式)
date: 2022-04-07 12:58:56
categories: 
- 高级前端笔记
tags: 
- vue
---


#### 数据响应式原理

响应式的意思是，当数据发生改变，视图会跟着改变，无需我们操作dom，响应式处理的入口是instance\init.js

响应式是从vue实例的_init()方法开始的，在_init()方法中先调用initState()方法初始化vue实例的状态，在其中调用了initData()，这个方法是把data属性注入到vue实例上，并且调用observe()把data对象转换成响应式的对象，observe()就是响应式的入口。

observe(value)接受一个参数，这个参数就是响应式要处理的对象，observe()在src/core/observer/index.js中，先判断value是否是对象，如果不是对象就直接返回。

然后判断value对象是否有__ob__属性，,如果有就说明这个对象已经做过响应式处理，也直接返回。如果没有，就为这个对象创建observer对象，然后把这个observer对象返回。
![3828aebf462cc43da6dc120bc960e503](1646185395364.jpg)

Observe类会给value对象定义一个不可枚举的__ob__属性，并把当前的observer对象记录到枚举__ob__里面来，然后进行数组和对象的响应式处理。
![3828aebf462cc43da6dc120bc960e503](56737F2A-8FF5-475B-AC0F-BFA25015690F.png)

数组的处理就是设置数组那几个特殊的方法，这些方法会改变数组，所以当这些方法被调用的时候，我们要发送通知，找到数组对应的__ob__，也就是observer对象，再找到observer对象中的dep方法调用，调用dep当中的notify方法。处理完这些方法后，遍历成员，对每一个成员调用observe，如果这个成员是对象的话，也会被转换为响应式的对象。 

对象的处理会调用walk方法，遍历对象所有属性，给每个属性调用defineReactive方法。
defineReactive会给每个属性创建dep对象，让dep去收集依赖，如果当前属性的值是对象，就调用observe，要把这个对象变成响应式。

defineReactive的核心就是定义getter和setter。
定义getter为每一个属性收集依赖，包括对象的子对象。
定义setter保存新值，如果新值是对象，就调用observe也转换成响应式，发送通知，就是调用dep.notify方法。
![266f9d19cdafcf66b22a546c361fd9ae](3A65CD6C-0740-42ED-9185-19234C239D9E.png)

依赖收集：首先执行watcher对象中的get方法，在get中调用pushTarget，在pushTarget中会把watcher对象记录到dep.target属性中。
在访问data中的成员的时候收集依赖，在访问值时就会触发defineReactive中的getter收集依赖，把属性对应的watcher添加到dep的subs数组中，也就是为属性收集依赖，如果属性的值是个对象，就创建一个childOb为这个子对象收集依赖，目的是子对象添加和删除成员时发送通知
![2d155fa9c0aa9b61d97d6488e0b84e85](E9D9E42D-6D38-4EDF-AE3B-69D7B782008A.png)

watcher：当数据发生变化的时候通过dep.notify发送通知，他会调用watcher的update方法，在update方法中会调用queueWatcher函数，来判断watcher是否被处理了，如果没有就添加到queue队列，并调用flushSchedulerQueue刷新队列

flushSchedulerQueue会触发beforeUpdata钩子函数，然后调用watcher.run()函数，在其中调用get()–>getter()–>updateComponent

调用run方法之后，就可以在页面上看到最新的数据了

然后就会清空上一次的依赖，重置watcher的状态，然后触发actived钩子函数，然后触发updated钩子函数
![2b9b14348506a056db1f6d3d2846069e](6733F301-2725-441A-AD21-4C54011B6FE2.png)



##### 异步更新队列

nexTick是在dom行程后获取内容，本身是一个微任务，运行环境不支持时降级为宏任务。数据更新到dom上之后，才会去执行nextTick中的回调函数。

nextTick静态方法是在global-api的index.js中被定义的，实例方法在instance下面的index.js中，在renderMixin()这个函数中，定义了nextTick。nextTick的核心，其实就是timerFunc()的处理。

在timerFunc()源码中我们可以得知：
如果支持 Promise 就用 Promise。如果不支持Promise
就用MutationObserver，MutationObserver 它会在指定的DOM发生变化时被调用。
如果不支持 MutationObserver 的话就用 setImmediate，但是这个特性只有最新版IE和node支持。
如果这些都不支持的话就用setTimeout。



#### 写在最后

原创博文, 如有错误, 敬请指导!