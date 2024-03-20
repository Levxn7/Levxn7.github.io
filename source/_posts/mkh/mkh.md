---
title: 模块化工具
date: 2022-01-15 11:23:22
categories: 
- 高级前端笔记
tags: 
- 模块化
- webpack
- rollup
- parcel
---
#### 模块打包工具由来
由于 ES Modules 存在环境兼容问题, 又模块文件较多, 网络请求比较频繁, 前端资源都需要模块化
新特性代码编译, 编译就是把es6编译成es5, 然后打包, 模块化JavaScript打包, 支持不同类型的资源模块

打包工具：解决的不只是js的模块化，而是前端整体的模块化
- webpack 模块加载器  代码拆分 资源模块
- rollup
- parcel


#### webpack
**webpack的基本使用**
webpack比较先进, 非常受欢迎
> yarn init \--yes
yarn add webpack webpack-cli \--dev
yarn webpack \--version
yarn webpack (会从index.js开始打包

**配置文件**
支持0配置
一般来说会将 src/index.js 作为打包入口, 将结果存入 dist/main.js 中, 但是也可以自己在 webpack.config.js 中设置
```js
const path = require('path')
module.exports = {
    mode: 'none',
    entry: './src/main.js', //设置输入
    output: { //设置输出
        filename: 'bundle.js',
        path: path.join(__dirname, 'output') //必须是绝对路径
    }
}
```

**打包模式**
使用cli设置打包模式 
> yarn webpack \--mode development //优化速度模式
> yarn webpack \--mode none //普通模式


**资源模块加载**
webpack内部默认只会打包js文件, 如果打包css需要安装loader来操作, webpack可将任何资源模块导入js一起打包
> yarn add css-loader \--dev //来打包
> yarn add style-loader \--dev //来调用
```js
module: {
rules: [ //加载规则
    {
        test: /.css$/,
        use: [
            'style-loader', //会从后往前执行
            'css-loader'
            ]
        }
        ]
    }
}
```

**文件资源加载器**
> yarn add file-loader \--dev //文件操作
```js
publicPath: 'dist/' //output

{ //rules
    test: /.png$/,
    use: 'file-loader'
}
```

**data url**
> yarn add url-loader \--dev //路径操作
```js
{ //rules
    test: /.png$/,
    use: {
        loader: 'url-loader',
        options: {
            limit: 10 * 1024 // 10 KB
         }
    }
}
```
小文件可以使用 Data URLs，可以减少请求次数, 大文件尽量单独提取存放, 可以提高加载速度

> 常用加载器分类
编译转换类
文件操作类
代码检查类

**处理es6**
因为模块打包需要，所以处理 import 和 export
> yarn add babel-loader @babel/core @babel/preset-env \--dev
```js
{ //rules
    test: /.js$/,
    use: {
        loader: 'babel-loader',
        options: {
            presets: ['@babel/preset-env']
        }
    }
},
```
Webpack只是打包工具, 加载器可以用来编译转换代码

兼容的模块加载方式有 import , require , define , 但是不要一个项目里写这么多种


**html-loader** 
默认只会处理img中的src属性, 所以处理options
```js
{
    test: /.html$/,
    use: {
        loader: 'html-loader',
        options: {
            attrs: ['img:src', 'a:href']
        }
    }
}
```

**Webpack模块加载方式**
·遵循ES Modules标准的import声明
·遵循CommonJS标准的require函数
·遵循AMD标准的define函数和require函数
·样式代码中的@import指令和url函数。 
·HTML代码中图片标签的src属性

**核心工作原理**
根据打包入口, 分析模块, 递归依赖树, 解析模块, 把结果放入bundle.js
Loader的机制是Webpack的核心

##### Loader
**Loader工作原理**
例如我们可以自己编写一个markdown文件的Loader
创建markdown-loader.js
export 一个函数, 返回结果必须是js代码
rules引用
安装marked模块解析
yarn add marked \--dev
可以自己处理成js返回
或html-loader来处理

Loader负责资源文件从输入到输出的转换, 它其实是一个管道的概念, 可以拼接使用

**插件机制**
plugin来解决除资源加载外, 其他自动化工作, 如清除, 拷贝, 压缩
> yarn add clean-webpack-plugin \--dev //清理
yarn add html-webpack-plugin \--dev //生成html
yarn add copy-webpack-plugin \--dev //不编译直接复制过去

```js
//注意删除publicpath
const { CleanWebpackPlugin } = require('clean-webpack-plugin') //解构
const HtmlWebpackPlugin = require('html-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')

plugins: [
    new webpack.ProgressPlugin(),
    new CleanWebpackPlugin(),
    //用于生成index.html
    new HtmlWebpackPlugin({
        title
        meta
        template
    })
    //用于生成iabout.html
    new HtmlWebpackPlugin({
        filename: 'about.html'
    }),
    new CopyWebpackPlugin([
        // 'public/**' 路径 通配符
        'public'
    ])
]
```


##### 开发一个插件
Plugin通过钩子机制实现
插件必须一个函数或者是一个包含apply方法的对象，所以一般是新建一个class，然后在class里构建一个apply方法
```js

class MyPlugin {
    apply (compiler) {
        console.log('MyPlugin 启动')
        compiler.hooks.emit.tap('MyPlugin', compilation => {
        // compilation => 可以理解为此次打包的上下文
        for (const name in compilation.assets) {
        // console.log(name)
        // console.log(compilation.assets[name].source())
        if (name.endsWith('.js')) {
        const contents = compilation.assets[name].source()
        const withoutComments = contents.replace(/\/\*\*+\*\//g, '')
        compilation.assets[name] = {
        source: () => withoutComments,
        size: () => withoutComments.length
        }
        }
        }
        })
    }
}
```
plugins中调用 new MyPlugin()
插件是通过在生命周期的钩子中挂载函数实现扩展



**如何增强webpack开发体验**
> yarn webpack \--watch //实现自动编译 \-- watch监听
自动刷新浏览器, 如果用browersync启动再监听, 会比较麻烦, 所以用 webpack-dev-server, 需注意该命令没有自动打包
yarn add webpack-dev-server \--dev //是个cli
yarn webpack-dev-server \--open //自动打开浏览器
注意: webpack-dev-server 访问静态资源需另外配置devServer
```js
devServer: {
    contentBase: './public',
},
```
webpack-dev-server 支持代理配置
```js
proxy: {
    '/api': {
    // http://localhost:8080/api/users -> https://api.github.com/api/users
    target: 'https://api.github.com',
    // http://localhost:8080/api/users -> https://api.github.com/users
    pathRewrite: {
    '^/api': ''
    },
    // 不能使用 localhost:8080 作为请求 GitHub 的主机名
    changeOrigin: true
    }
}
```

**Source Map (.map后缀名）**
靠在结尾添加注释来引入Source Map, Source Map解决了源代码与运行代码不一致所产生的问题, webpack也可以通过配置属性devtool生成.map文件 
```js
devtool: 'source-map',
```
![1623729629215.jpg](1623729629215.jpg)
选择合适的source-map
开发环境选择c-m-e-s-m
我的代码每行不会超过80个字符
我的代码经过 Loader转换过后的差异较大
首次打包速度慢无所谓，重写打包相对较快

生产模式选择none, 因为source map会暴露源代码, 毕竟调试是开发阶段的事, 如果没有信心就nosources-s-m


**开启hmr热更新**
自动刷新导致的页面状态丢失
HMR 热更新体验非常友好
HMR是Webpack中最强大的功能之一
极大程度的提高了开发者的工作效率

hmr已经集成在 webpack-dev-server中 不需要安装
> yarn webpack-dev-server \--hot //使用命令行或通过配置开启
```js
const webpack = require('webpack')

devServer: {
    hot: true
    // hotOnly: true // 只使用 HMR，不会 fallback 到 live reloading
},
plugins: [
    new webpack.HotModuleReplacementPlugin()
]
```
这样只能自动更新样式, 而样式是因为style-loader来操作的, hmr没有通用的js替换方案
hmr注意事项

生产环境优化 不同环境下的配置
1.配置文件根据环境不同导出不同配置 （适用于中小型项目）
2.一个环境对应一个配置文件 yarn add webpack-merge
```js
"scripts": {
    "build": "webpack --config webpack.prod.js"
},
```

内置插件 definePlugin 可以为代码注入一些可能会变化的值 如api

**tree-shaking**
摇掉未引用代码, 会在生产模式下自动开启, 使用如下
```js
optimization: {
    // 模块只导出被使用的成员 导出 但不引用（标记枯树枝）
    usedExports: true,
    // 尽可能合并每一个模块到一个函数中 （作用域提升）
    concatenateModules: true,
    // 压缩输出结果 删除未引用成员 （摇掉枯树枝）
    // minimize: true
}
```

tree-shaking 的前提是必须使用 ES Modules 的代码, 最新版本的babel不会导致树摇失效, 如果不确定就写入这些↓
```js
options: {
    presets: [
    // 如果 Babel 加载模块时已经转换了 ESM，则会导致 Tree Shaking 失效
    // ['@babel/preset-env', { modules: 'commonjs' }]
    // ['@babel/preset-env', { modules: false }]
    // 也可以使用默认配置，也就是 auto，这样 babel-loader 会自动关闭 ESM 转换
    ['@babel/preset-env', { modules: 'false' }]
    ]
}
```

**sideEffects**
标记副作用, 生产环境自动开启

**代码分割**
代码分割就是分包, 如果bundle体积过大会浪费流量和带宽, 但分割时资源不要太大也不要太碎

**多入口打包**
适用于多页面程序, 把entry改成对象
```js
entry: {
    index: './src/index.js',
    album: './src/album.js'
},
output: {
    filename: '[name].bundle.js'
},

optimization: {
    splitChunks: {
        // 自动提取所有公共模块到单独 bundle
        chunks: 'all'
    }
},

plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
        title: 'Multi Entry',
        template: './src/index.html',
        filename: 'index.html',
        chunks: ['index']
    }),
    new HtmlWebpackPlugin({
        title: 'Multi Entry',
        template: './src/album.html',
        filename: 'album.html',
        chunks: ['album']
    })
]
```

**动态导入** 
动态导入的模块会被自动分包, 是按需加载的, 如果是vue或者react单页面开发的话, 就可以在路由映射组件实现这种按需加载
```js
// import posts from './posts/posts' 不再用这种方式导入
// import album from './album/album'
const render = () => {
    const hash = window.location.hash || '#posts'
    const mainElement = document.querySelector('.main')
    mainElement.innerHTML = ''
    if (hash === '#posts') {
        // mainElement.appendChild(posts()) 
        import(/* webpackChunkName: 'components' */'./posts/posts').then(({ default: posts }) => { //魔法注释
        mainElement.appendChild(posts())
    })
    } else if (hash === '#album') {
        // mainElement.appendChild(album())
        import(/* webpackChunkName: 'components' */'./album/album').then(({ default: album }) => {
        mainElement.appendChild(album())
    })
    }
}
```

**提取css到单个文件** 
超过150kb才会考虑单独提取
> yarn add mini-css-extract-plugin \--dev
yarn add optimize-css-assets-webpack-plugin \--dev
```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const TerserWebpackPlugin = require('terser-webpack-plugin')

optimization: {
    minimizer: [
        new TerserWebpackPlugin(),
        new OptimizeCssAssetsWebpackPlugin()
    ]
},

module: {
    rules: [
        {
        test: /\.css$/,
        use: [
            // 'style-loader', // 将样式通过 style 标签注入
            MiniCssExtractPlugin.loader,
            'css-loader'
        ]
        }
    ]
},

plugins: [
    new MiniCssExtractPlugin()
]
```

**输出文件名hash**
[hash] //整体hash
[chunkhash] //文件hash
[contenthash:8] //hash位数


#### rollup
rollup 也是 ES Modules 的打包器, 但仅仅是ES Modules打包器. 与webpack非常类似, 但小巧的多, 它不支持hmr这种高级功能, 会默认开启树摇
> yarn add rollup \--dev  //可以提供一个cli
yarn rollup ./src/index.js \--format iife(自调用函数） \--file dist/bundle.js

**配置文件**
rollup.config.js , 导出的是一个对象
```js
export default {
    input: 'src/index.js',
    output: {
        file: 'dist/bundle.js',
        format: 'iife'
    }
}
```
yarn rollup \--config //启动打包

**扩展途径**
插件是 Rollup唯一扩展途径
> yarn add rollup-plugin-json \--dev
```js
import json from 'rollup-plugin-json'

export default {
    plugins: [
        json() //放入的是调用结果
    ]
}
```

**加载npm模块**
>yarn add rollup-plugin-node-resolve \--dev
```js
import resolve from 'rollup-plugin-node-resolve'

export default {
    plugins: [
        resolve()
    ]
}
```
 
 
**加载common.js**
> yarn add rollup-plugin-commonjs \--dev
```js
import commonjs from 'rollup-plugin-node-resolve'

export default {
    plugins: [     
        commonjs()
    ]
}
```

**代码拆分** 
使用的方式是动态导入
```js
import('./logger').then(({ log }) => {
    log('code splitting~')
})

export default {
    input: 'src/index.js',
    output: {
        // file: 'dist/bundle.js',
        // format: 'iife'
        dir: 'dist',
        format: 'amd'
    }
}
```

**多入口打包**  
公共部分会自动提取, 将input修改为数组或对象就可以了
```js
export default {
    // input: ['src/index.js', 'src/album.js'],
    input: {
        foo: 'src/index.js',
        bar: 'src/album.js'
    },
    output: {
        dir: 'dist',
        format: 'amd'
    }
}
```
页面不能直接引用amd格式的输出文件，要用require来引用
```html
<!-- AMD 标准格式的输出 bundle 不能直接引用 -->
<!-- <script src="foo.js"></script> -->
<!-- 需要 Require.js 这样的库 -->
<script src="https://unpkg.com/requirejs@2.3.6/require.js" data-main="foo.js"></script>
```


**rollup与webpack**
优点
·输出结果更加扁平
·自动移除未引用代码
·打包结果依然完全可读

缺点
·加载非 ESM的第三方模块比较复杂
·模块最终都被打包到一个函数中，无法实现 HMR
·浏览器环境中,代码拆分功能依赖AMD库

Webpack 大而全，Rollup 小而美
应用开发使用建议 Webpack
库/框架开发使用建议 Rollup


#### parcel
parcel 是零配置前端应用打包器, 官方建议使用html作为打包的入口文件
>yarn add parcel-bundler \--dev
yarn parcel src/index.html
yarn parcel build src/index.html

优点: 
打包同时会启动服务器
支持热特换
支持自动安装依赖
支持动态导入
构建速度非常快（多进程）



#### 写在最后

原创博文, 如有错误, 敬请指导!

