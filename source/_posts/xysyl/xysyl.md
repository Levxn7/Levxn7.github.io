---
title: 响应式原理
date: 2021-09-19 15:37:19
categories: 
- 高级前端笔记
tags: 
- vue
- 响应式原理
- 发布订阅模式
- 观察者模式
---

#### 响应式原理
·数据响应式
·数据模型仅仅是普通的JavaScript 对象，而当我们修改数据时，视图会进行更新，避免了繁琐的DOM操作，提高开发效率
·双向绑定
·数据改变，视图改变;视图改变，数据也随之改变·我们可以使用v-model在表单元素上创建双向数据绑定·数据驱动是Vue最独特的特性之一
·开发过程中仅需要关注数据本身，不需要关心数据是如何渲染到视图

**深入响应式原理**
vue2.x中的响应式原理是基于defineProperty 来实现的, ie8以下不支持
数据劫持：当访问或者设置 vm 中的成员的时候，做一些干预操作
其中有三个参数
```js
let data = {
    msg: 'hello'
}

// 模拟 Vue 的实例
let vm = {}

// 数据劫持：当访问或者设置 vm 中的成员的时候，做一些干预操作
Object.defineProperty(vm, 'msg', {
    // 可枚举（可遍历）
    enumerable: true,
    // 可配置（可以使用 delete 删除，可以通过 defineProperty 重新定义）
    configurable: true,
    // 当获取值的时候执行
    get () {
        console.log('get: ', data.msg)
        return data.msg
    },
    // 当设置值的时候执行
    set (newValue) {
        console.log('set: ', newValue)
        if (newValue === data.msg) {
            return
        }
        data.msg = newValue
        // 数据更改，更新 DOM 的值
        document.querySelector('#app').textContent = data.msg
    }
})
```

3.0是基于 proxy 实现, 直接监听对象而非属性, 是es6中新增的, ie不支持,性能由浏览器优化
proxy是一个构造函数 通过new来实例
```js
let data = {
    msg: 'hello',
    count: 10
}

// 模拟 Vue 的实例
let vm = {}

proxyData(data)

function proxyData(data) {
    // 遍历 data 对象的所有属性
    Object.keys(data).forEach(key => {
    // 把 data 中的属性，转换成 vm 的 setter/setter
    Object.defineProperty(vm, key, {
        enumerable: true,
        configurable: true,
        get () {
            console.log('get: ', key, data[key])
            return data[key]
        },
        set (newValue) {
            console.log('set: ', key, newValue)
            if (newValue === data[key]) {
                return
            }
            data[key] = newValue
            // 数据更改，更新 DOM 的值
            document.querySelector('#app').textContent = data[key]
        }
    })
    })
}
```
2比1简洁的多  性能也要好一点

**发布订阅模式**
vue的自定义事件  
```js
// Vue 自定义事件
let vm = new Vue()
// { 'click': [fn1, fn2], 'change': [fn] }

// 注册事件(订阅消息)
vm.$on('dataChange', () => {
    console.log('dataChange')
})

vm.$on('dataChange', () => {
    console.log('dataChange1')
})
// 触发事件(发布消息)
vm.$emit('dataChange')
```


发布订阅模式

订阅者$on  发布者$emint 信号中心 let vm = new Vue()
```js
// 事件触发器
class EventEmitter {
    constructor () {
    // { 'click': [fn1, fn2], 'change': [fn] }
    this.subs = Object.create(null)
    }

    // 注册事件
    $on (eventType, handler) {
    this.subs[eventType] = this.subs[eventType] || []
    this.subs[eventType].push(handler)
    }

    // 触发事件
    $emit (eventType) {
    if (this.subs[eventType]) {
        this.subs[eventType].forEach(handler => {
        handler()
        })
    }
    }
}

// 测试
let em = new EventEmitter()
em.$on('click', () => {
    console.log('click1')
})
em.$on('click', () => {
    console.log('click2')
})

em.$emit('click')
```

观察者模式 没有事件中心  发布者知道订阅者的存在
```js
// 发布者-目标
class Dep {
    constructor () {
    // 记录所有的订阅者
    this.subs = []
    }
    // 添加订阅者
    addSub (sub) {
    if (sub && sub.update) {
        this.subs.push(sub)
    }
    }
    // 发布通知
    notify () {
    this.subs.forEach(sub => {
        sub.update()
    })
    }
}
// 订阅者-观察者
class Watcher {
    update () {
    console.log('update')
    }
}

// 测试
let dep = new Dep()
let watcher = new Watcher()

dep.addSub(watcher)

dep.notify()
```
总结
.观察者模式是由具体目标调度，比如当事件触发，Dep 就会去调用观察者的方法，所以观察者模式的订阅者与发布者
之间是存在依赖的。
.发布/订阅模式由统-调度中心调用，因此发布者和订阅者不需要知道对方的存在。
![观察者模式和发布订阅模式](QQ20210625-163604@2x.png)


#### 响应式部分代码实现 
部分解释写在注释中,代码下载地址<https://github.com/Levxn7/lagouhomework/tree/master/part3/fed-e-task-03-01/second>

> html
```html
<!DOCTYPE html>
<html lang="cn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Mini Vue</title>
</head>
<body>
  <div id="app">
    <div v-html="html"></div>
    <button v-on:click="clicked">点击</button>
    <!-- <button v-on="{click:click}">点击</button> -->
    <!-- ,mouseenter:onenter,mouseleave:leave -->
    <h1>差值表达式</h1>
    <h3>{{ msg }}</h3>
    <h3>{{ count }}</h3>
    <h1>v-text</h1>
    <div v-text="msg"></div>
    <h1>v-model</h1>
    <input type="text" v-model="msg">
    <input type="text" v-model="count">
  </div>
  <script src="./js/dep.js"></script>
  <script src="./js/watcher.js"></script>
  <script src="./js/compiler.js"></script>
  <script src="./js/observer.js"></script>
  <script src="./js/vue.js"></script>
  <script>
    let vm = new Vue({
      el: '#app',
      data: {
        msg: 'Hello Vue',
        count: 100,
        person: { name: 'zs' },
        html: "<h1>这是一个h1标签</h1>"
      },
      methods:{ 
        clicked:function(){
            console.log("被点击了");
        }
      }
    })
    console.log(vm.msg)
    // vm.msg = { test: 'Hello' }
    vm.test = 'abc'
  </script>
</body>
</html>
```

> dep.js
```js
class Dep {
  constructor () {
    // 存储所有的观察者
    this.subs = []
  }
  // 添加观察者
  addSub (sub) {
    if (sub && sub.update) {
      this.subs.push(sub)
    }
  }
  // 发送通知
  notify () {
    this.subs.forEach(sub => {
      sub.update()
    })
  }
}
```
> watcher.js
```js
class Watcher {
  constructor (vm, key, cb) {
    this.vm = vm
    // data中的属性名称
    this.key = key
    // 回调函数负责更新视图
    this.cb = cb

    // 把watcher对象记录到Dep类的静态属性target
    Dep.target = this
    // 触发get方法，在get方法中会调用addSub
    this.oldValue = vm[key]
    Dep.target = null
  }
  // 当数据发生变化的时候更新视图
  update () {
    let newValue = this.vm[this.key]
    if (this.oldValue === newValue) {
      return
    }
    this.cb(newValue)
  }
}
```
> compiler.js
```js
class Compiler {
  constructor (vm) {
    this.el = vm.$el
    this.vm = vm
    this.methods = vm.$methods
    this.compile(this.el)
  }
  // 编译模板，处理文本节点和元素节点
  compile (el) {
    let childNodes = el.childNodes
    Array.from(childNodes).forEach(node => {
      // 处理文本节点
      if (this.isTextNode(node)) {
        this.compileText(node)
      } else if (this.isElementNode(node)) {
        // 处理元素节点
        this.compileElement(node)
      }

      // 判断node节点，是否有子节点，如果有子节点，要递归调用compile
      if (node.childNodes && node.childNodes.length) {
        this.compile(node)
      }
    })
  }
  // 编译元素节点，处理指令
  compileElement (node) {
    // console.log(node.attributes)
    // 遍历所有的属性节点
    Array.from(node.attributes).forEach(attr => {
      // 判断是否是指令
      let attrName = attr.name
      if (this.isDirective(attrName)) {
        // v-text --> text
        attrName = attrName.substr(2)
        let key = attr.value
        this.update(node, key, attrName)
      }
    })
  }

  update (node, key, attrName) {
    let updateFn
    if (attrName.substr(0,3) === 'on:') {
      updateFn = this[attrName.substr(0,2) + 'Updater']
      updateFn && updateFn.call(this, node, this.methods[key], attrName.substr(3))
    } else {
      updateFn = this[attrName + 'Updater']
      updateFn && updateFn.call(this, node, this.vm[key], key)
    }
  }

  // 处理 v-text 指令
  textUpdater (node, value, key) {
    node.textContent = value
    new Watcher(this.vm, key, (newValue) => {
      node.textContent = newValue
    })
  }
  // v-model
  modelUpdater (node, value, key) {
    node.value = value
    new Watcher(this.vm, key, (newValue) => {
      node.value = newValue
    })
    // 双向绑定
    node.addEventListener('input', () => {
      this.vm[key] = node.value
    })
  }
  // v-html
  htmlUpdater (node, value, key) {
    node.innerHTML = value
    new Watcher(this.vm, key, (newValue) => {
      node.innerHTML = newValue
    })
  }
  // v-on
  onUpdater (node, value, key) {
    if (key === "click") {
      node.onclick = value
      new Watcher(this.methods, key, (newValue) => {
        node.onclick = newValue
      })
    }
  }

  // 编译文本节点，处理差值表达式
  compileText (node) {
    // console.dir(node)
    // {{  msg }}
    let reg = /\{\{(.+?)\}\}/
    let value = node.textContent
    if (reg.test(value)) {
      let key = RegExp.$1.trim()
      node.textContent = value.replace(reg, this.vm[key])

      // 创建watcher对象，当数据改变更新视图
      new Watcher(this.vm, key, (newValue) => {
        node.textContent = newValue
      })
    }
  }
  // 判断元素属性是否是指令
  isDirective (attrName) {
    return attrName.startsWith('v-')
  }
  // 判断节点是否是文本节点
  isTextNode (node) {
    return node.nodeType === 3
  }
  // 判断节点是否是元素节点
  isElementNode (node) {
    return node.nodeType === 1
  }
}
```
> observe.js
```js
class Observer {
  constructor (data) {
    this.walk(data)
  }
  walk (data) {
    // 1. 判断data是否是对象
    if (!data || typeof data !== 'object') {
      return
    }
    // 2. 遍历data对象的所有属性
    Object.keys(data).forEach(key => {
      this.defineReactive(data, key, data[key])
    })
  }
  defineReactive (obj, key, val) {
    let that = this
    // 负责收集依赖，并发送通知
    let dep = new Dep()
    // 如果val是对象，把val内部的属性转换成响应式数据
    this.walk(val)
    Object.defineProperty(obj, key, {
      enumerable: true,
      configurable: true,
      get () {
        // 收集依赖
        Dep.target && dep.addSub(Dep.target)
        return val
      },
      set (newValue) {
        if (newValue === val) {
          return
        }
        val = newValue
        that.walk(newValue)
        // 发送通知
        dep.notify()
      }
    })
  }
}
```
> vue.js
```js
class Vue {
  constructor (options) {
    // 1. 通过属性保存选项的数据
    this.$options = options || {}
    this.$data = options.data || {}
    this.$methods = options.methods || {}
    this.$el = typeof options.el === 'string' ? document.querySelector(options.el) : options.el
    // 2. 把data中的成员转换成getter和setter，注入到vue实例中
    this._proxyData(this.$data)
    // 3. 调用observer对象，监听数据的变化
    new Observer(this.$data)
    // 4. 调用compiler对象，解析指令和差值表达式
    new Compiler(this)
  }
  _proxyData (data) {
    // 遍历data中的所有属性
    Object.keys(data).forEach(key => {
      // 把data的属性注入到vue实例中
      Object.defineProperty(this, key, {
        enumerable: true,
        configurable: true,
        get () {
          return data[key]
        },
        set (newValue) {
          if (newValue === data[key]) {
            return
          }
          data[key] = newValue
        }
      })
    })
  }
}
```

#### 写在最后

原创博文, 如有错误, 敬请指导!