---
title: 手写一个Promise的内部基本实现
date: 2021-12-06 15:53:41
categories: 
- 前端进阶笔记
tags: 
- Promise
---

参照异步编程那篇博文我们知道，Promise 是 JS 异步编程中的重要概念，异步抽象处理对象，是目前比较流行 Javascript 异步编程解决方案之一.

今天我们就来尽可能还原 Promise 中的几个基本 API，并通过注释简单描述思路和原理.


```js
//Promise有三个状态,且状态为成功和失败时不可再更改,我们且定义三个常量
const Pending = 'pending'
const Resolve = 'fulFilled'
const Rejected = 'rejected'
class myPromise {
    //执行器 
    //promise就是一个类,在执行这个类的时候需要传递一个执行器,执行器就是传入的那个回调函数,执行器会立即执行
    constructor (ex){
        try {
            return ex(this.resolve，this.reject)
        } catch (e){
            this.reject(e)
        }
    }
    
    //声明变量
    status = Pending
    value = undefined
    reason = undefined
    //定义成功回调和失败回调的数组
    scb = []
    fcb = []

    resolve = value =>  {
        //如果是Pending状态再继续执行 改状态 赋值等操作
        if (this.status !== Pending) return
        this.status = Resolve
        this.value = value
        // this.scb && this.scb(this.value)
        //循环执行
        while (this.scb.length) this.scb.shift()()
    }
    reject = reason =>  {
        if (this.status !== Pending) return
        this.status = Rejected
        this.reason = reason
        // this.fcb && this.fcb(this.reason)
        while (this.fcb.length) this.fcb.shift()()
    }

    then(scb，fcb) {
        //处理then链式调用情况
        scb = scb ? scb : value => value
        fcb = fcb ? fcb : reason => { throw reason}
        let p2 = new myPromise((resolve，reject) => {
            //分三种状态判断
            if (this.status === Resolve) {
                //setTimeout为了可以异步获取p2
                setTimeout(() => {
                    try {
                        resPro(scb(this.value)，resolve，reject，p2)
                    } catch (error) {
                        reject(error)
                    }
                }，0);
            } else if (this.status === Rejected) {
                setTimeout(() => {
                    try {
                        resPro(fcb(this.reason)，resolve，reject，p2)
                    } catch (error) {
                        reject(error)
                    }
                }，0);
            } else {
                this.scb.push(() => {
                    setTimeout(() => {
                        try {
                            resPro(scb(this.value)，resolve，reject，p2)
                        } catch (error) {
                            reject(error)
                        }
                    }，0);
                })
                this.fcb.push(() => {
                    setTimeout(() => {
                        try {
                            resPro(fcb(this.reason)，resolve，reject，p2)
                        } catch (error) {
                            reject(error)
                        }
                    }，0);
                })
            }
        })
        return p2

    }
    static resolve(value){
        //如果是primose类型就直接返回, 如果是普通值就包裹一层promise返回
        if(value instanceof myPromise) return value
        return new myPromise(resolve => resolve(value))
    }
    static all(arr) {
        let result = []
        let sum = 0
        return new myPromise((resolve，reject) => {
            function add (i，t) {
                result[i] = t
                sum++
                if (sum === arr.length) {
                    resolve(result) 
                }
            }
            for(let i = 0; i<arr.length; i++){
                let cur = arr[i]
                if (cur instanceof myPromise ) {
                    //如果是promise值
                    cur.then(res => add(i，res)，rej => reject(rej))
                } else {
                    //如果是普通值
                    add(i，cur)
                }
            }
        })
    }
    static race(arr) {
        let sum = 0
        return new myPromise((resolve，reject) => {
            function addresolve (t) {
                sum++
                if (sum === 1) {
                    resolve(t) 
                    return
                }
            }
            function addreject (t) {
                sum++
                if (sum === 1) {
                    reject(t) 
                    return
                }
            }
            for(let i = 0; i<arr.length; i++){
                let cur = arr[i]
                if (cur instanceof myPromise ) {
                    //promise
                    cur.then(res => addresolve(res)，rej => addreject(rej))
                } else {
                    //普通值
                    addresolve(cur)
                }
            }
        })
    }
    catch(fcb) {
        return this.then(undefined，fcb)
    }
    finally(cb) {
        return this.then(res => {
            //等待cb执行完成
            return myPromise.resolve(cb()).then(() => res)
        },rej => {
            return myPromise.resolve(cb()).then(() => {throw rej})
        })
    }
}

function resPro(x，resolve，reject，p2) {
    //如果和本身一样就抛出错误
    if (p2 === x) {
        return reject(new TypeError('循环'))
    }
    //如果是promise就执行  如果不是就包一层
    if (x instanceof myPromise ) {
        x.then(resolve，reject)
    } else {
        resolve(x)
    }
}

```

下面我们开始测试

先定义一个函数，要想创建一个 promise 对象、可以使用 new 来调用 Promise 的构造器来进行实例化.

```js
let pro = new myPromise(function (resolve，reject) {
    resolve('resolve1')
})
```

Promise 构造函数包含一个参数和一个带有 resolve（解析）和 reject（拒绝）两个参数的回调.在回调中执行一些操作（例如异步），如果一切都正常，则调用 resolve，否则调用 reject.
对于已经实例化过的 promise 对象可以调用 promise.then() 方法，传递 resolve 和 reject 方法作为回调.

##### 测试.then()链式调用情况

经测试，下面代码打印结果为: resolve1
说明.then()方法的参数是可选的，当参数为空时，promise会把结果返回给下一个then来接收
```js
pro.then().then().then(resolve => console.log(resolve)，reject => console.log(reject))
```

##### 测试循环调用的情况

```js
let p1 = pro.then(value => {
    console.log(value)
    return p1
});
p1.then(value => {
    console.log(value)
}，reason => console.log(reason.message))
```
这一段打印结果是: resolve1 循环
证明我们循环调用处理的是没什么问题的

****

为了方便测试.all()和.race()方法，我们先定义三个函数
```js
function p1 () {
    return new myPromise(function (resolve，reject) {
      setTimeout(function () {
        resolve('p1')
      }，1000)
    })
  }
function p2 () {
    return new myPromise(function (resolve，reject) {
        resolve('p2');  
    })
}
function p3 () {
    return new myPromise(function (resolve，reject) {
        reject('失败')
    })
}
```

##### .all()

Promise.all 方法接受一个数组作为参数，p1、p2、p3 都是 Promise 对象的实例.（Promise.all 方法的参数不一定是数组，但是必须具有 iterator 接口，且返回的每个成员都是 Promise 实例.）

.all()的状态分成两种情况.
- 只有p1、p2/p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时入参的返回值组成一个数组，传递给p的回调函数.
- 只要p1、p2/p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数.

此时我们测试.all()函数，运行结果为: alla,b,p1,p2,c
```js
myPromise.all(['a'，'b'，p1()，p2()，'c']).then( res => console.log('all' + res),rej => console.log('all' + rej))
```
如果我们给.all加入一个失败的参数，运行结果为: all失败
```js
  myPromise.all(['a'，'b'，p1()，p3()，'c']).then( res => console.log('all' + res),rej => console.log('all' + rej))
```
结合测试可以证明我们.all()方法实现的是没有问题的.

##### .race()

.race()只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理.

此时我们测试.race()函数，传入两个不同速度的函数的情况，运行结果为: racep2
```js
myPromise.race([p1()，p2()]).then( res => console.log('race' + res),rej => console.log('race' + rej))
```
此时我们测试传入函数和常量的情况，运行结果为: racea
```js
myPromise.race(['a'，p2()]).then( res => console.log('race' + res),rej => console.log('race' + rej))
```
如果我们给.race()加入一个失败的参数，运行结果为: race失败
```js
myPromise.race([p1()，p3()]).then( res => console.log('race' + res),rej => console.log('race' + rej))
```
结合测试可以证明我们.race()方法实现也是基本可以的.

##### .resolve

有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用.

此时我们测试.resolve()函数，如果传入一个promise则返回这个promise，运行结果为: p1
```js
myPromise.resolve(p1()).then( res => console.log(res))
```
如果传入一个常量则返回一个promise包裹这个常量，运行结果为: 100
```js
myPromise.resolve(100).then( res => console.log(res))
```

##### .finally()

.finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作.它接受一个普通的回调函数作为参数，该函数不管怎样都必须执行.

promise状态为成功的情况
```js
p2().then(res => {
    console.log(res)
},rej=>console.log(rej))
.finally(() => {
    console.log('res')
}).
```
运行结果为: p2 res

promise状态为失败的情况
```js
p3().then(res => {
    console.log(res)
},rej=>console.log(rej))
.finally(() => {
    console.log('finally')
})
```
运行结果为: 失败 res

##### .catch()

.catch()方法用于指定发生错误时的回调函数.Promise 对象的错误具有"冒泡"性质，会一直向后传递，直到被捕获为止.也就是说，错误总是会被下一个 catch 语句捕获.

```js
p3().then(res => {
    console.log(res)
}).catch(rea => {
    console.log('rea'+rea)
})
```
运行结果为: rea失败


#### 写在最后

原创博文, 如有错误, 敬请指导!