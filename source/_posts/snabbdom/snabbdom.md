---
title: snabbdom
date: 2021-07-15 15:37:29
categories: 
- 前端进阶笔记
tags: 
- snabbdom
---
mkdir snabdom-demo

cd snabdom-demo

//创建package

npm init -y

//本地安装parcel

npm install parcel-bundler -D

配置scripts

```js
"scripts" :{
"dev" :"parcel index.html -- open",
"build": "parcel build index.html"
}
```

在根目录创建index.html文件  来引入

```html
    <div id="app"></div>
    <script src="./src/01-basicusage.js"></script>
```



npm install snabbdom@2.1.0

h('!') //这是 一个空的节点



#### snabbdom模块的使用

```js
// 1. 导入模块
import { styleModule } from 'snabbdom/build/package/modules/style'
import { eventListenersModule } from 'snabbdom/build/package/modules/eventlisteners'

// 2. 注册模块
const patch = init([
  styleModule,
  eventListenersModule
])

// 3. 使用h() 函数的第二个参数传入模块中使用的数据（对象）
let vnode = h('div', [
  h('h1', { style: { backgroundColor: 'red' } }, 'Hello World'),
  h('p', { on: { click: eventHandler } }, 'Hello P')
])

function eventHandler () {
  console.log('别点我，疼')
}

let app = document.querySelector('#app')
// 生成虚拟dom 根据diff算法更新dom
patch(app, vnode)
```


snabbdom源码学习

snabbdom源码地址

<https://github.com/snabbdom/snabbdom>


Snabbdom的核心
init() 设置模块，创建patch()函数
使用h()函数创建JavaScript对象(VNode)描述真实DOM
patch()比较新旧两个Vnode
把变化的内容更新到真实DOM树


#### 一个简单的例子
总体代码在<https://github.com/Levxn7/lagouhomework/tree/master/part3/fed-e-task-03-01/third>
```js
import { init } from 'snabbdom/build/package/init';
import { h } from 'snabbdom/build/package/h';
import { styleModule } from 'snabbdom/build/package/modules/style';
import { eventListenersModule } from 'snabbdom/build/package/modules/eventlisteners';


let patch = init([styleModule, eventListenersModule]);

let vnode;
let sortBy = "rank";

let data = []
for (let i = 0; i < 10; i++) {
  let a = {
    rank: i,
    title: 'title' + i,
    desc: 'this is desc' + i
  }
  data.push(a)
}

function change(prop) {
  data.sort((a, b) => {
    if (a[prop] > b[prop]) {
      return 1;
    }
    if (a[prop] < b[prop]) {
      return -1;
    }
    return 0;
  });

  vnode = patch(vnode, view(data));
}

function add() {
  let a = {
    rank: data.length,
    title: 'title' + data.length,
    desc: 'this is desc' + data.length
  }
  data.unshift(a)
  vnode = patch(vnode, view(data));
}

function remove(index) {
  data.splice(index, 1)
  vnode = patch(vnode, view(data));
}

function movieView(movie,index) {
  return h(
    "div.li",
    {
      key: movie.rank,
      style: {
        padding: '0 16px',
        height: '50px',
        display: 'flex',
        alignItems: 'center',
        borderBottom: '1px solid #ccc',
        justifyContent: 'space-between',
        opacity: '0',
        delayed: { transition: 'opacity 2s', opacity: '1' },
        remove: { transition: 'opacity 0.3s', opacity: '0' }
      },
    },
    [
      h("div", { style: { fontWeight: "bold" } }, movie.rank),
      h("div", movie.title),
      h("div", movie.desc),
      h(
        "button.btn.rm-btn",
        {
          on: {
            click: () => {
              remove(index);
            },
          },
        },
        "删除"
      ),
    ]
  );
}

function view(data) {
  return h("div", [
    h("h1", "Top 10 movies"),
    h("div", [
      h("button.add", { on: { click: add } }, " 添加 "),
      "排序方式: ",
      h("span.btn-group", [
        h(
          "button.rank",
          {
            class: { active: sortBy === "rank" },
            on: {
              click: () => {
                change("rank");
              },
            },
          },
          " Rank "
        ),
        h(
          "button.title",
          {
            class: { active: sortBy === "title" },
            on: {
              click: () => {
                change("title");
              },
            },
          },
          " Title "
        ),
        h(
          "button.desc",
          {
            class: { active: sortBy === "desc" },
            on: {
              click: () => {
                change("desc");
              },
            },
          },
          " Description "
        ),
      ]),
    ]),
    h(
      "div.list",
      { style: { height: "8px" } },
      data.map(movieView)
    ),
  ]);
}

const addEl = document.querySelector('#app');
vnode = patch(addEl, view(data));

```

#### 写在最后

原创博文, 如有错误, 敬请指导!