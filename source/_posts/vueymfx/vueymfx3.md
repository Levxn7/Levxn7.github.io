---
title: vue源码复习(虚拟DOM)
date: 2022-03-02 12:58:56
categories: 
- 前端进阶笔记
tags: 
- vue
---


#### 虚拟DOM
vue中的虚拟dom的实现借鉴了snabdom这个虚拟dom库，如模块机制、钩子函数、diff算法，在此基础上额外添加了指令和组件等。虚拟dom是使用JS对象来描述真实dom，虚拟dom就是js对象。

为什么要使用虚拟dom：
避免直接操作真实dom，提高开发效率。可以作为一个中间层来跨平台。在复杂试图的情况下可以提高渲染性能，简单视图的情况下可能会增加开销。

在v-for的时候可以给每个节点增加key属性，key只能是原始值，不能是对象。因新老节点对比顺序不同，设置key会比不设置key的dom操作少很多。


##### 源码部分
**虚拟dom创建的整体过程**
这个过程是接着vue的首次渲染过程来讲的，在updateComponent()中我们调用了render()函数和_update()函数。

在render中我们调用了用户传入的render函数或者是模板编译成的render函数，如果是用户传入的render函数的话，就调用vm.$createElement()方法，如果是模板编译成的render的话，调用的是vm.\_c()方法。不管vm.$createElement()还是vm.\_c(),他最后都会执行createElement方法。在createElement中处理了参数的差异，最终调用了_createElement()方法。在这个方法中创建了vnode对象，最终返回vnode。
![0295fbf3548dfa0579eecb29a23f05b2](D36D403B-B738-4A3A-A620-8EA128A49D54.png)

在update函数是负责把虚拟dom渲染成真实dom，在update里边会调用__patch__方法。如果是首次执行，patch的第一个参数是$el，也就是一个真实dom。如果是数据更新那就传入两个vnode，第一个prevVnode也就是上次保存下来的vnode。
patch函数是在runtime下的index.js中初始化的，他是给vue原型上挂载了__patch__这个方法，所有的vue实例都可以访问到，他其实就是runtime下的patch.js模块中导出的patch函数。在这个函数中设置了moudles和nodeOps两个对象，moudles中存储的是一些模块，nodeOps是用来操作dom的。设置好后会把这两个对象传递给createPatchFunction()函数，这个函数最终返回的是我们需要的patch函数。
![6cb8377bdd89d8d0cfa09019b227d13f](D3317FFA-E67E-47BD-B87B-FB3A7070CF97.png)

patch是vdom/patch.js中的createPatchFunction()返回的函数，在createPatchFunction中首先做了初始化的事情，她里面有很多的辅助函数，特别定义了cbs对象。在cbs对象中存储了所有模块中定义的那些钩子函数，这些钩子函数是为了处理节点的属性、事件、样式等。
在patch中还会判断第一个参数是不是真实dom，如果是真实dom的话说明是首次加载。这时会把真实dom转化为vnode，然后去调用createElm，把newVnode转换为真实dom，挂载到dom树上来。如果是数据更新的时候，就要通过sameVnode函数判断新旧vnode是否是相同的节点。如果不是相同节点的话，就执行其中的patchVnode，也就是diff算法。patch执行结束之后，删除旧节点。
![c193a5cb93662967e61d92f5bd7f38cb](96EE883B-C1B1-431A-8A07-163D7BC713E4.png)

createElm函数是用来把虚拟节点转换为真实dom，并挂载到dom树上。他也会把虚拟节点的子节点children，转换为真实节点，挂载到dom树上。
patchVnode是用来对比新旧vnode，以及新旧vnode的子节点，然后更新他们的差异。如果新旧vnode都有子节点，并且子节点不同的话，还会调用updateChildren去对比子节点的差异。
updateChildren会去处理新旧子节点，在处理的过程中优化了这个操作。他会先去把新旧节点的头和尾取出来进行比较，总共有4种比较方式。在比较的过程中，如果我们的节点是sameVnode，那就会调用patchVnode打补丁。如果这4种比较的方式都不满足的话，就会在老节点的子节点种查找newStartVnode，也就是新的开始节点在老的节点中是否存在，并进行处理。如果新节点比老节点多，会把新增的节点插入到dom中，如果老节点比较多，就把多余的老节点删除。
![cfa4cb62a5b1b3adb9e1cffdcccf6a08](5DA71656-C1C3-4A04-B539-B1287BE68A6B.png)

#### 模板编译和组件化
模板编译的作用，就是把模板中的内容（html）转换为渲染函数。.vue文件是webpack通过vue-loader在构建的过程中转换成render函数的。

compileToFunctions()是模板编译的入口函数，他的内部先从缓存加载编译好的render渲染函数，如果缓存中没有，就调用compile开始编译。
在compile中先合并选项（options），然后调用baseCompile编译模板。compile的核心是合并选项，真正的处理是在baseCompile中完成的。
把模板和合并好的选项传递给baseCompile，他做了三件事情。一、使用parse函数把template模板转换成AST tree（抽象语法树）。二、使用optimze函数对AST tree进行优化：标记所有AST tree中的sub trees（静态根节点），sub trees不需要每次被重绘，patch的过程会跳过sub trees。三、将优化过的AST tree生成字符串形式的js代码。
当compile执行完毕以后，会回到入口函数compileToFunctions中，他继续把字符串形式的代码转换为函数的形式，通过执行createFunction，当render和staticRenderFuns创建完毕，最终他们都会被挂载到vue实例中的options选项对应的属性上。
注：
模板编译的过程中，会标记静态根节点，对静态根节点进行优化处理，重新渲染的时候不需要渲染静态根节点，因为他们不会发生改变。另外在模板中不要多写无意义的空格和换行，生成AST对象时会保留这些空格和换行，他们都会被存储到内存中。
![a28ffcef5fccd93a5778d8f185793c20](967222E7-DF80-458C-AF98-6F5A4CF032BD.png)

##### 组件化
一个vue组件就是一个拥有预定义选项的一个vue实例。
一个组件可以组成页面上一个功能完备的区域，组件可以包含脚本、样式、模板



#### 写在最后

原创博文, 如有错误, 敬请指导!