---
title: vue源码复习(首次渲染e)
date: 2022-03-01 12:58:56
categories: 
- 前端进阶笔记
tags: 
- vue
---


#### 基础结构

第一种： 创建实例时，传入el和data选项，vue内部会把data数据传到el所对应的模板中，并把模板渲染到浏览器
第二种： render方法接受一个h函数，h函数的作用是创建一个虚拟dom，render函数把h创建的虚拟dom返回。$mount的作用是把虚拟dom转化为真实dom，渲染到浏览器。
![bd52306c3a68ee0dfc0386b158fc99c3](E8F7BCFC-02FA-4B11-A113-16A25255BC33.png)


#### 生命周期

vue生命周期分为8个阶段，即分别是创建前
beforeCreate、创建后created、载入前beforeMount、载入后mounted、更新前beforeUpdate、更新后updated、销毁前beforeDestroy、销毁后destroyed。
![2765e8b954d15199b8ca4966defe94d2](4AE06B6F-6BA5-4829-9066-00019FEE3763.png)


#### 语法和概念
差值表达式
指令
计算属性和侦听器(computed和watch)
    - 要大量逻辑运算的时候，计算属性的结果会被缓存，下次再需要的时候会从缓存拿结果。如果需要监听数据变化并进行复杂操作，使用监听器。
class和style绑定
条件渲染(v-if)和列表渲染(v-for)
表单输入绑定
组件（可复用的vue实例）
插槽（slot，组件更灵活）
插件
混入（mixin，让组件重用的一种方式）
深入响应式原理
不同构件版本的vue

### vue源码剖析
vue2中引入了flow静态类型检查器，vue3是typescript

vue的不同构建版本
![58196a21b3ba7ace05428db39ae4c4b8](74762267-B729-48BF-B7BA-6277CE72462B.png)

vue的静态成员和实例成员初始化过程在目录中源码的部分是src文件夹：
compiler（编译器）把模板转化为render函数
core（vue的核心）
- components中定义了vue自带的keep-alive组件
- global-api：initGlobalAPI方法初始化vue的静态方法
- instance：.创建了vue构造函数2.初始化vue实例部分
- observer响应式部分
- vdom虚拟dom

platform 平台相关的代码
server服务端渲染相关
sfc单文件组件
\-------------------
#### 入口文件
从script文件夹下的config文件中查找builds对象，其对象下写了各个构建版本的部分信息：包括入口，出口，模块化方式等，所以得知入口文件在src/platform/web下
打包的过程中，如果没有render，就会把template转化为render函数。如果传递了render，就会调用mount方法渲染dom


#### 初始化过程
注册指令：
所有的指令都保存在Vue.options.directives里，所有的组件都保存在Vue.options.components里。都是全局内可以访问的。

给Vue挂载__patch__函数，用来把虚拟dom转化为真实dom。
给Vue注册一个$mount方法，用来渲染dom
23
**core/instance**
初始化构造方法：指向core/instance
    + initMixin() 注册_init()方法来初始化vm
    + stateMixin() 给原型挂载$data $props属性 $set $delete $watch方法
    + eventsMixin() 利用发布订阅模式注册事件相关方法：$on $once $off $emit
    + lifecycleMixin() 生命周期相关的混入方法 \_update $forceUpdate $destory
    + renderMixin() $nextTick() \_render(调用的用户传入的render）
    
**core/global-api**
初始化静态成员： 指向core/global-api
    + 初始化config对象，并挂载方法nextTick set delete
    + 设置响应式对象
    + 初始化vue.options对象并扩展，用来存储全局的components，directives，filters
    + 设置keep-alive全局组件
    + 注册Vue.use()来注册插件
    + 注册Vue.mixin()来实现混入
    + 注册Vue.extend()基于传入的options返回一个组件的构造函数
    + 注册Vue.directive() Vue.component() Vue.filter()
    
#### 首次渲染过程


在首次渲染之前，先进行vue的初始化，初始化实例成员，静态成员。当初始化结束后调用vue的构造函数，在构造函数中调用了_init()方法，这个方法相当于整个文件的入口，方法中调用了$mount()

第一个\$mount（entry-runtime-with-compiler.js中的）是入口文件$mount，通过compileToFunctions()帮我们把模板编译成render()渲染函数，存入options.render中

第二个\$mount（runtime\index.js中的）中重新获取\$el并调用mountComponent()，先判断是否是render选项，如果没有，但是传入的是模板并且是运行版本时，版本会被警告不支持编译器

触发beforemount，定义updateComponent

创建watcher对象，传入了updateComponent函数，创建完watcher会调用一个get方法，在get中调updateComponent，updateComponent中会调用render和update两个方法

render的作用是创建虚拟dom，最终调用的用户传入的或者模板编译的render返回vnode，调用vm.\_update，在其中调用__patch__ 这个方法将虚拟dom转换成真实dom并挂载到页面上来，真实dom渲染到$el中

触发mounted 挂载结束 返回vue实例

![62954824e5d7d1645e674798997297e6](34E5255B-F8F3-4424-ABFC-ADCFF160486E.png)
![7fee6d20c4962409267eb71a325cfdfa](A90B15BB-0364-4B29-BA2A-2BB6D3D4A595.png)
![fc61d97566402dd356dd3a8d25902f98](0625729F-EBC8-4926-A8D2-FED25F91B09E.png)



#### 写在最后

原创博文, 如有错误, 敬请指导!