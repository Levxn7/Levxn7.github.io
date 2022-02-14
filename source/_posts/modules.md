---
title: modules
date: 2021-06-08 12:01:47
categories: 
- 前端进阶笔记
tags: 
- modules
- 模块化
---
#### 模块化
内容概要
·模块化演变过程
·模块化规范
·常用的模块化打包工具
·基于模块化工具构建现代Web应用

##### 演变过程
1: 文件划分方式
没有私人空间
污染全局作用域
命名冲突问题
无法管理模块依赖关系

2: 命名空间方式
包裹全局对象中
私有变量不安全

3: lIFE
闭包 私有变量安全

以上是在早期没有工具和规范的情况下, 对模块化的落地方式


**模块化规范的出现**
模块化标准＋模块加载器

commonjs规范: CommonJS是以同步模式加载模块
·一个文件就是一个模块
·每个模块都有单独的作用域
·通过module.exports导出成员
·通过require函数载入模块

AMD (Asynchronous Module Definition) 异步模块定义规范
.AMD使用起来相对复杂
·模块JS文件请求频繁

require.js: 使用define定义模块, 使用require加载模块, 我理解为它和早期的angularjs很像

Sea.is + CMD 后被require兼容了


##### 模块化标准规范
浏览器使用ES Modules标准规范, nodejs使用commonJs标准来规范
ES Modules是当下最主流的规范了

ES Modules基本特性
自动采用严格模式，忽略'use strict', 每个ES Modules模块都是单独的私有作用域, ES Modules是通过CORS去请求外部JS模块的, ES Modules的 script标签会延迟执行脚本


**导入导出**
使用export导出, 使用import导入, 使用as输出重命名, 如:
```js
export {
    hello as fooHello
}
```

> 注意
虽然长得一样, 但导出的不是字面量, 导入的不是解构
导出的是引用关系, 不是值
导入的成员是只读, 不能更改

**import用法**
引入不能省略后面的扩展名
相对路径以./开头, 绝对路径以/开头, 或者使用完整的url
{}为空时就只加载那个模块, 不需要引用进来
\* as mod引入所有成员 
不能from变量, 只能写在最上面
动态加载模块
```js
import('./module.js').then(function (module) {
  console.log(module)
})
```
提取默认成员
```js
import { name, age, default as title } from './module.js'
import abc, { name, age } from './module.js'
```
直接导出导入的成员
```js
export { foo, bar } from './module.js'
```

**polyfill**
IE不能兼容ES Modules, 就只能引入unpkg的js地址
nomodule属性只在不支持ES Modules环境中运行, 所以可以拿nomodule来引入unpkg, 否则就会在能兼容ES Modules的环境里执行两次

**node中使用ES Modules的实验**
node版本>8.5就可以使用ES Modules, 需要把文件后缀的.js换成.mjs
使用命令>node --experimental-modules index.mjs 来执行


**ES Modules与CommonJS**
ES Modules中可以导入CommonJS模块, 但CommonJS中不能导入ES Modules模块, CommonJS 始终只会导出一个默认成员

ES Modules与CommonJS差异
```js
//因ES Modules没有直接获取路径的方法, 所以用以下方法获取路径
import { fileURLToPath } from 'url'import { dirname } from 'path'
const _filename = fileURLToPath( import.meta.url)console.log(__filename)
const _dirname = dirname(__filename)console.log(_dirname)
```

如果我们在package.json中写入type: 'modules' 就可以不用mjs的扩展名了, 修改回.js扩展名
不过这样的话, ES Modules中的CommonJS需要后缀名修改为.cjs才能运行

早期的node可以使用babel支持ES Modules
```js
>yarn add ababel/node ababel/core ababel/preset-env --dev
>yarn babel-node
>yarn babel-node index.js --presets=ababel/preset-env
```
或在.babelrc 中写入
```js
    {" presets" : ["@babel/preset-env" ]}
```
就可以>yarn babel-node index.js执行了

还可以单独安装插件, 然后在 .babelrc 中引入
```js
{
"plugins":[
    "@babel/plugin-transform-modules-commonjs"1]
}
```
然后>yarn babel-node index.js 就可以直接执行上面那一长串命令了




#### 写在最后

原创博文, 如有错误, 敬请指导!


