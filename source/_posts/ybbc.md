---
title: 异步编程
date: 2021-05-26 17:21:33
categories: 
- 前端进阶笔记
tags: 
- Promise
---

概要：
- 同步模式与异步模式
- 事件循环与消息队列
- 异步编程的几种方式
- Promise异步方案、宏任务/微任务队列
- Generator异步方案、Async / Await语法糖

#### 什么是同步模式和异步模式
同步函数：当一个函数是同步执行时，那么当该函数被调用时不会立即返回，直到该函数所要做的事情全都做完了才返回.调用栈（工作表）,消息循环(eventloop)，把全部代码放入匿名函数去执行, 如果某一功能执行时间过长，后面就会阻塞（卡死），所以需要异步函数.

异步函数：如果一个异步函数被调用时，该函数会立即返回尽管该函数规定的操作任务还没有完成.含有异步函数的代码的执行顺序会混乱, 代码不会等待程序执行完, 同步函数执行完了才会去执行异步函数.

异步函数h调用时，调用方不等被调方返回结果就转身离去，因此必须有一种机制让被调方有了结果时能通知调用方.在同一进程中有很多手段可以利用，常用的手段是回调、event 对象和消息.

回调方式很简单：调用异步函数时在参数中放入一个函数地址，异步函数保存此地址，待有了结果后回调此函数便可以向调用方发出通知.如果把异步函数包装进一个对象中，可以用事件取代回调函数地址，通过事件处理例程向调用方发通知.

回调函数是所有异步编程方案的根基，但大量嵌套回调不可取(回调地狱).

> 小tips: 使用setTimeout 0 可以把同步代码变为异步代码


#### promise
promise的几个特点
- promise是一个对象，对象和函数的区别就是对象可以保存状态，函数不可以（闭包除外）.
- 并未剥夺函数return的能力，因此无需层层传递callback，进行回调获取数据.
- 代码风格，容易理解，便于维护.
- 多个异步等待合并便于解决.

##### 一个最最简单的promise
```js
new Promise(resolve => {
  setTimeout(() => {
    resolve('hello')
  }, 2000)}).then(res => {
  console.log(res)})
```

> promise的使用案例(基本应用)
```js
//使用promise实现ajax
function ajax (url) {
  return new Promise(function (resolve, reject) {
    var xhr = new XMLHttpRequest()
    xhr.open('GET', url)
    xhr.responseType = 'json'
    xhr.onload = function () {
      if (this.status === 200) {
        resolve(this.response)
      } else {
        reject(new Error(this.statusText))
      }
    }
    xhr.send()
  })
}

ajax('/api/foo.json').then(function (res) {
  console.log(res)
}, function (error) {
  console.log(error)
})
```


##### promise有三个状态：pending, fulfilled, rejected.

promise状态一经改变，就不会再改变了.且状态改变只有两种可能：
- 从pending变为fulfilled
- 从pending变为rejected.

这两种情况只要发生，状态就凝固了，不会再变了.

当promise状态发生改变，就会触发then()里的响应函数处理后续步骤；

promise.then()方法有两个参数, resolve和reject.

resolve作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
reject作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去.




##### 链式调用
promise在使用中要避免回调嵌套，尽量保持扁平化.

- Promise对象的then方法会返回一个全新的 Promise对象
- 后面的then方法就是在为上一个then返回的 Promise注册回调
- 前面then方法中回调函数的返回值会作为后面then方法回调的参数
- 如果回调中返回的是 Promise，那后面then方法的回调会等待它的结束


**.then()**
1、接收两个函数作为参数，分别代表fulfilled（成功）和rejected（失败）
2、.then()返回一个新的Promise实例，所以它可以链式调用
3、当前面的Promise状态改变时，.then()根据其最终状态，选择特定的状态响应函数执行
4、状态响应函数可以返回新的promise，或其他值，不返回值也可以我们可以认为它返回了一个null；
5、如果返回新的promise，那么下一级.then()会在新的promise状态改变之后执行
6、如果返回其他任何值，则会立即执行下一级.then()
7、.then()里面的实参应该是函数.如果不是函数,就无视它’

> .then()里面有.then()的情况
> - 因为.then()返回的还是Promise实例
> - 会等里面的then()执行完，再执行外面的
> - 对于我们来说，此时最好将其展开，也是一样的结果，而且会更好读

##### 异常处理

Promise会自动捕获内部异常，并交给rejected响应函数处理.

第一种：reject('错误信息').then(() => {}, () => {错误处理逻辑})
第二种：throw new Error('错误信息').catch( () => {错误处理逻辑})
推荐使用第二种方式，更加清晰好读，并且可以捕获前面所有的错误（可以捕获N个then回调错误）
.then onFulfilled
.catch onRejected
应该在代码中明确捕获每一个可能存在的异常 而不是丢给全局去处理

##### 并行执行
> .all()

Promise.all 方法接受一个数组作为参数，p1、p2、p3 都是 Promise 对象的实例.（Promise.all 方法的参数不一定是数组，但是必须具有 iterator 接口，且返回的每个成员都是 Promise 实例.）

.all()的状态分成两种情况.
- 只有p1、p2/p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时入参的返回值组成一个数组，传递给p的回调函数.
- 只要p1、p2/p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数.

```javascript
let p1 = new Promise((resolve, reject) => {
  resolve('成功了')})

let p2 = new Promise((resolve, reject) => {
  resolve('success')})

let p3 = Promse.reject('失败')

Promise.all([p1, p2]).then((result) => {
  console.log(result)     
  }).catch((error) => {
  console.log(error)
  })          
  //['成功了', 'success']

Promise.all([p1,p3,p2]).then((result) => {
  console.log(result)
  }).catch((error) => {
  console.log(error)   
  })   
  // 失败了，打出 '失败'
```
  
> .race()

.race()只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理.

```javascript
let p1 = new Promise((resolve, reject) => {
  setTimeout(function () {
        resolve('p1')
      }，1000)
  })

let p2 = new Promise((resolve, reject) => {
  resolve('p2')
  })

let p3 = Promse.reject('失败')

Promise.race([p1, p2]).then((result) => {
  console.log(result)     
  }).catch((error) => {
  console.log(error)
  })          
  //p2跑的更快 打印p2

Promise.race([p1,p3,p2]).then((result) => {
  console.log(result)
  }).catch((error) => {
  console.log(error)   
  })   
  // 失败了，打出 '失败'
```

##### 静态方法
> .resolve方法

有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用.

此时我们测试.resolve()函数，如果传入一个promise则返回这个promise，运行结果为: p1
```js
myPromise.resolve(p1()).then( res => console.log(res))
```
如果传入一个常量则返回一个promise包裹这个常量，运行结果为: 100
```js
myPromise.resolve(100).then( res => console.log(res))
```

##### 执行时序/宏任务vs微任务
宏任务是由宿主发起的，而微任务由JavaScript自身发起。
在ES3以及以前的版本中，JavaScript本身没有发起异步请求的能力，也就没有微任务的存在。在ES5之后，JavaScript引入了Promise，这样，不需要浏览器，JavaScript引擎自身也能够发起异步任务了。
回调任务中的任务称为宏任务，要重新排队.
promise中的任务是微任务，可直接执行.（process.nextTick/MutationObserver也是微任务）
所以，总结一下，两者区别为：

<table>
	<tr>
	    <th></th>
	    <th>宏任务（macrotask）</th>
	    <th>微任务（microtask）</th>  
	</tr >
	<tr>
	    <td >谁发起的</td>
	    <td >宿主（Node、浏览器）</td>
	    <td >JS引擎</td>
	</tr>
	<tr >
	    <td rowspan="5">具体事件</td>
	    <td>1. script (可以理解为外层同步代码)</td>
	    <td>1. Promise</td>
	</tr>
	<tr>
	    <td>2. setTimeout/setInterval</td>
	    <td>2. MutaionObserver</td>
	</tr>
	<tr>
	    <td>3. UI rendering/UI事件</td>
	    <td>3. Object.observe（已废弃；Proxy 对象替代）</td>
	</tr>
	<tr>
	    <td>4. postMessage，MessageChannel</td>
	    <td>4. process.nextTick（Node.js）</td>
	</tr>
	<tr><td>5. setImmediate，I/O（Node.js）</td>
	    <td></td>
	</tr>
	<tr>
	    <td >谁先运行</td>
	    <td>后运行</td>
	    <td>先运行</td>
	</tr>
	<tr>
	    <td >会触发新一轮Tick吗</td>
	    <td >会</td>
	    <td >不会</td>
	</tr>
</table>

#### Generator异步方案（es2015）
> 这部分我会在其他博文中详细写到.届时贴链接过来.

生成器函数, 就是函数名前多个*
调用不会马上执行, 但会得到一个生成器对象
调用.next()时执行
执行到函数里yield那一行, 把yield的值作为对象中的value返回
再次调用.next()如果有参数会从yield继续执行, 参数作为yield的值
.throw（）会抛出异常  执行.catch
.done 布尔  是否结束生成器
扩展: co库


#### async/await是生成器的语法糖
> 这部分我会在其他博文中详细写到.届时贴链接过来.

是语言层面的异步编程标准.



#### 写在最后
参考资料: <https://www.jianshu.com/p/bfc3e319a96b>

原创博文, 如有错误, 敬请指导!