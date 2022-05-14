---
title: 实现防抖和节流的函数
date: 2021-09-03 14:33:14
categories: 
- 前端进阶笔记
tags: 
- 性能优化
- 内存管理
---

##### 我们为什么需要防抖和节流：
浏览器默认情况下都会有自己的监听事件间隔（ 4~6ms)，如果检测到多次事件的监听执行，那么就会造成不必要的资源浪费
在一些高频率事件触发的场景下我们不希望对应的事件处理函数多次执行
比如以下场景:
- 滚动事件
- 输入的模糊匹配
- 轮播图切换
- 点击操作
- ....

前置的场景： 界面上有一个按钮，我们可以连续多次点击

防抖：对于这个高频的操作来说，我们只希望识别一次点击，可以人为是第一次或者是最后一次
节流：对于高频操作，我们可以自己来设置频率，让本来会执行很多次的事件触发，按着我们定义的频率减少触发的次数

##### 实现一个防抖函数
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>防抖函数实现</title>
</head>

<body>
  <button id="btn">点击</button>
  <script>
    var oBtn = document.getElementById('btn')
    // oBtn.onclick = function () {
    //   console.log('点击了')
    // }

    /** 
     * handle 最终需要执行的事件监听
     * wait 事件触发之后多久开始执行
     * immediate 控制执行第一次还是最后一次，false 执行最后一次
    */
    function myDebounce(handle, wait, immediate) {

      // 参数类型判断及默认值处理
      if (typeof handle !== 'function') throw new Error('handle must be an function')
      if (typeof wait === 'undefined') wait = 300
      if (typeof wait === 'boolean') {
        immediate = wait
        wait = 300
      }
      if (typeof immediate !== 'boolean') immediate = false

      // 所谓的防抖效果我们想要实现的就是有一个 ”人“ 可以管理 handle 的执行次数
      // 如果我们想要执行最后一次，那就意味着无论我们当前点击了多少次，前面的N-1次都无用
      let timer = null
      return function proxy(...args) {
        let self = this,
          init = immediate && !timer
        clearTimeout(timer)
        timer = setTimeout(() => {
          timer = null
          !immediate ? handle.call(self, ...args) : null
        console.log(self)
        console.log(...args)
        }, wait)

        // 如果当前传递进来的是 true 就表示我们需要立即执行
        // 如果想要实现只在第一次执行，那么可以添加上 timer 为 null 做为判断
        // 因为只要 timer 为 Null 就意味着没有第二次....点击
        init ? handle.call(self, ...args) : null
      }

    }

    // 定义事件执行函数
    function btnClick(ev) {
      console.log(this)
      console.log('点击了1111', this, ev)
    }

    // 当我们执行了按钮点击之后就会执行...返回的 proxy
    oBtn.onclick = myDebounce(btnClick, 200, false)
    // oBtn.onclick = btnClick()  // this ev

  </script>
</body>

</html>
```


##### 实现一个节流函数
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>节流函数实现</title>
  <style>
    body {
      height: 5000px;
    }
  </style>
</head>

<body>
  <script>
    // 节流：我们这里的节流指的就是在自定义的一段时间内让事件进行触发

    function myThrottle(handle, wait) {
      if (typeof handle !== 'function') throw new Error('handle must be an function')
      if (typeof wait === 'undefined') wait = 400

      let previous = 0  // 定义变量记录上一次执行时的时间 
      let timer = null  // 用它来管理定时器

      return function proxy(...args) {
        let now = new Date() // 定义变量记录当前次执行的时刻时间点
        let self = this
        let interval = wait - (now - previous)

        if (interval <= 0) {
          // 此时就说明是一个非高频次操作，可以执行 handle 
          clearTimeout(timer)
          timer = null
          handle.call(self, ...args)
          previous = new Date()
        } else if (!timer) {
          // 当我们发现当前系统中有一个定时器了，就意味着我们不需要再开启定时器
          // 此时就说明这次的操作发生在了我们定义的频次时间范围内，那就不应该执行 handle
          // 这个时候我们就可以自定义一个定时器，让 handle 在 interval 之后去执行 
          timer = setTimeout(() => {
            clearTimeout(timer) // 这个操作只是将系统中的定时器清除了，但是 timer 中的值还在
            timer = null
            handle.call(self, ...args)
            previous = new Date()
          }, interval)
        }
      }

    }

    // 定义滚动事件监听
    function scrollFn() {
      console.log('滚动了')
    }

    // window.onscroll = scrollFn
    window.onscroll = myThrottle(scrollFn, 600)
  </script>
</body>

</html>
```
