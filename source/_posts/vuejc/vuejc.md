---
title: vue基础
date: 2021-06-25 12:58:56
categories: 
- 前端进阶笔记
tags: 
- vue
---

#### 基础语法

##### 生命周期
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。比如说created ,mounted ,updated等钩子函数。
![vue生命周期](1408390-20190502182244199-1878074396.png)

##### 内置指令
```js
/*
1.v-bind:响应并更新DOM特性; 例如：v-bind:href v-bind:class v-bind:title 等等; 主要用法是绑定属性，动态更新HTML元素上的属性；
2.v-on:用于监听DOM事件; 例如:v-on:click v-on:keyup 
3.v-model:数据双向绑定;用于表单输入等;例如:< input v-model= "message">
4.v-show:条件渲染指令,为DOM设置css的style属性
5.v-if:条件渲染指令,动态在DOM内添加或删除DOM元素
6、v-else：条件渲染指令，必须跟v-if成对使用
7、v-else-if：判断多层条件，必须跟v-if成对使用；
8、v-text：更新元素的textContent；例如：< span v-text="msg"></ span>等同于 < span>{{msg}} </ span>；
9、v-html：更新元素的innerHTML；会把标签名也带上。
10、v-for：循环指令；
11、v-cloak：不需要表达式，这个指令保持在元素上直到关联实例结束编译； v-cloak 是一个解决初始化慢导致页面闪动的最佳实践 ；
12、v-once：也是一个不需要表达式的指令，作用是定义它的元素或组件只渲染一次，包括元素或组件的所有子节点。首次渲染后，不再随数据的变化重新渲染，将被视为静态内容； v-once 在业务中也很少使用，当你需要进一步优化性能时，可能会用到。 
13、v-pre：不需要表达式，跳过这个元素以及子元素的编译过程，以此来加快整个项目的编译速度；例如： < span v-pre>{{ this will not be /compiled }} </ span>；
*/
```
具体使用可参考: <https://my.oschina.net/u/3970421/blog/2964249>

##### 计算属性
计算属性就是当其依赖属性的值发生变化时，这个属性的值会自动更新，与之相关的DOM部分也会同步自动更新。对于任何复杂逻辑，你都应当使用计算属性。

计算属性和侦听器
Class和Style绑定
条件渲染/列表渲染表单输入绑定

以上内容的官网解释: <https://cn.vuejs.org/v2/guide/computed.html>

还有组件 插槽 插件 混入等功能

##### 路由
Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。路由实际上就是可以理解为指向，就是我在页面上点击一个按钮需要跳转到对应的页面，这就是路由跳转；

首先我们来学习三个单词（route，routes，router）：
route：首先它是个单数，译为路由，即我们可以理解为单个路由或者某一个路由；
routes：它是个复数，表示多个的集合才能为复数；即我们可以理解为多个路由的集合，JS中表示多种不同状态的集合的形式只有数组和对象两种，事实上官方定义routes是一个数组；所以我们记住了，routes表示多个数组的集合；
router：译为路由器，上面都是路由，这个是路由器，我们可以理解为一个容器包含上述两个或者说它是一个管理者，负责管理上述两个；举个常见的场景的例子：当用户在页面上点击按钮的时候，这个时候router就会去routes中去查找route，就是说路由器会去路由集合中找对应的路由；

##### 路由的简单使用
通过vue.use来注册组件
如果是一个函数, 直接调用函数做组件, 如果是对象的话, 会调用对象的install方法来注册插件

**创建路由的步骤**
1 路由规则
2 创建路由对象 传入路由规则
3 mainjs引入
4 app.vue添加站位
5 创建连接

**动态路由**
懒加载
推荐使用props来传参（还有$route.params.id）

**嵌套路由**
很多时候我们的页面结构决定了我们可能需要嵌套路由，比如当我们进入主页之后有分类，然后当选择其中一个分类之后进入对应的详情，这个时候我们就可以用到嵌套路由；官方文档中给我们提供了一个children属性，这个属性是一个数组类型，里面实际放着一组路由；这个时候父子关系结构就出来了，所以children属性里面的是路由相对来说是children属性外部路由的子路由；
```js
const routes=[
    {
        path:'/page1',
        component:page1,
        children: [
            {
                path: "phone",// /page1/phone
                component: phone
            },
            {
                path: "computer", // /page1/computer
                component: computer
            },
        ]
    }
]
```

**路由导航两种方式**
> 标签导航

标签导航<router-link><router-link>是通过转义为<a></a>标签进行跳转，其中router-link标签中的to属性会被转义为a标签中的href属性；
```html
//跳转到名为user路由，并传递参数userId
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
 ```
> 编程式导航

我们可以通过this.$router.push()这个方法来实现编程式导航，当然也可以实现参数传递，这种编程式导航一般是用于按钮点击之后跳转
```js
router.push({ path: '/user/:userId', params: { userId: 123 }})
//push replace go(-2) 等方法
```

##### hash模式和history模式
· Hash模式式是基于锚点，以及onhashchange事件, 需要使用问号拼接
例: https:/music.163.com/#/playlist?id=3102961863

· History模式是基于 HTML5 中的 History API（需要服务端配置支持）
history.pushState() IE10以后才支持
history.replaceState()
例: https://music.163.com/playlist/3102961863

**实现原理**
Hash模式
·URL中#后面的内容作为路径地址
·监听hashchange 事件
·根据当前路由地址找到对应组件重新渲染
History模式
·通过history.pushState()方法改变地址栏监听popstate事件
·根据当前路由地址找到对应组件重新渲染

**模拟实现** 
手写router 参考视频3-1-2  10-19小节 (部分解释以注释的方式写在以下代码块中)
```js
let _Vue = null
export default class VueRouter {
  static install (vue) {
    // 1 判断当前插件是否被安装
    if (VueRouter.install.installed) return
    VueRouter.install.installed = true
    // 2 把Vue的构造函数记录在全局
    _Vue = vue
    // 3 把创建Vue的实例传入的router对象注入到Vue实例
    // _Vue.prototype.$router = this.$options.router
    _Vue.mixin({
      beforeCreate () {
        if (this.$options.router) {
          _Vue.prototype.$router = this.$options.router
        }
      }
    })
  }

  constructor (options) {
    this.mode = options.mode || "hash"
    this.routes = options.routes
    this.routeMap = {}
    // observable
    this.data = _Vue.observable({
        //存储当前的路由地址, 默认是/
      current: '/'
    })
    this.init()
  }
  //包装方法
  init () {
    this.createRouteMap()
    this.initComponents(_Vue)
    this.initEvent()
  }
  createRouteMap () {
    //遍历所有路由信息，把组件和路由的映射记录到 routeMap 对象中
    this.routes.forEach( route => {
      this.routeMap[route.path] = route.component
    })
  }
  initComponents(Vue){
    //创建 router-link 和 router-view 组件
    const self = this
    Vue.component('router-link',{
      props:{
        to:String
      },
      //运行时版本的解决办法
      render(h){
        return h("a",{
          attrs:{
            href: self.mode === "history" ? "" : "#" + this.to
          },
          //给a标签注册事件
          on:{
            click:self.mode === "history" ? this.clickhander : this.hashClickhander
          }
        },[this.$slots.default])
      },
      methods:{
        clickhander(e){
            //参数: 事件 网页标题, 地址
          history.pushState({},"",this.to)
          this.$router.data.current = this.to
          e.preventDefault()
        },
        hashClickhander(e){
          this.$router.data.current=this.to
        }
      }
      // 运行时版本的vue不支持模版
      // template:"<a :href='to'><slot></slot></a>"
    })

    Vue.component('router-view',{
      render(h){
          //获取路由组件 h帮我们转换成虚拟dom
        let cm = self.routeMap[self.data.current]
        return h(cm)
      }
    })
  }
  initEvent(){
      
    //注册 popstate 事件，当路由地址发生变化，重新记录当前的路径
    const eventType = this.mode === 'history' ? 'popstate' : 'hashchange'
    const handle = this.mode === 'history' ? this.getLocation : this.getHash
    window.addEventListener(eventType,handle.bind(this))
  }
  getLocation(){
    this.data.current = window.location.pathname
  }
  getHash(){
    let href = window.location.href
    const index = href.indexOf('#')
    if (index < 0) return '/'
    this.data.current = href.slice(index + 1)
  }
}
```



#### 写在最后

原创博文, 如有错误, 敬请指导!