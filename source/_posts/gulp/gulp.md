---
title: 实现一个gulp模块并封装成cli
date: 2021-06-07 12:47:14
categories: 
- 高级前端笔记
tags: 
- gulp
- 自动化构建案例
---
#### 一个简单的gulp案例
接下来我们通过一个实际的案例, 来看如何使用gulp完成一个网页应用自动化构建工作流
首先拿到一个需要构建的项目, 打开项目
打开项目: code pages-boilerplate
安装gulp: yarn add gulp --dev
新建gulpfile.js文件
导入gulp的api
```js
const { src, dest } = require('gulp')
```
注意: public下是不需要构建加工的文件，src是开发阶段的代码 都需要构建，sass es6都需要构建 图片需要压缩


##### 样式的编译
定义一个style, 然后导出
```js
const style = () => {
    return src('src/assets/styles/*.scss', { base: 'src'}) //base校准保存路径
    .pipe(dest('temp'))
}
module.exports = {
    style,
}
```
yarn gulp style  经过测试可以打包成功

安装转换流: yarn add gulp-sass --dev（内部会安装node-sass）
```js
const sass = require('gulp-sass')
```
使用转换: 
```js
const style = () => {
    return src('src/assets/styles/*.scss', { base: 'src'})
    .pipe(sass({ outputstyle: 'expanded'}))//完全展开的形式
    .pipe(dest('temp'))
}
```
yarn gulp style  经过测试可以打包成功
> 注意: sass会认为_开头的文件是被引用的文件 所以不会进行转换
 sass({ outputstyle: 'expanded'}) 是完全展开的格式, 否则 } 会在 ；后面 而不是另起一行, 如
 ```js
 .icon-aperture:before {
  content: '\e900'; }
  ```


##### 脚本的编译
定义一个script 然后导出
```js
const script = () => {
    return src('src/assets/scripts/*.js', { base: 'src'})
    .pipe(dest('temp'))
}
module.exports = {
    script,
}
```
安装babel环境: yarn add gulp-babel --dev
安装转换流: yarn add @babel/core @babel/preset-env --dev
```js
const babel = require('gulp-babel')
```
```js
const script = () => {
    return src('src/assets/scripts/*.js', { base: 'src'})
    .pipe(babel({ presets: ['@babel/preset-env'] }))
    .pipe(dest('temp'))
}
```
> 里面这句话不要丢, babel只是一个平台不做转换, presets才是插件, 是新特性的一个整体的打包, s使用它来做转换, 如果不写这句话输出的文件几乎不会变格式

yarn gulp script


##### 模板文件的编译
定义一个page, 然后导出
```js
const page = () => {
    return src('src/**/*.html', { base: 'src'}) 
    .pipe(dest('temp'))
}

module.exports = {
    page
}
```
安装转换流: yarn add gulp-swig --dev
```js
const swig = require('gulp-swig')

const page = () => {
    return src('src/**/*.html', { base: 'src'}) // 通配符可以这样写 'src/** / * .html'
    .pipe(swig())
    .pipe(dest('temp'))
}
```
把数据部分的数组放在data中导入，或使用json文件式导入
```js
const data = {
  menus: [
    {
      name: 'Home',
      icon: 'aperture',
      link: 'index.html'
    },
    {
      name: 'Features',
      link: 'features.html'
    },
    {
      name: 'About',
      link: 'about.html'
    },
    {
      name: 'Contact',
      link: '#',
      children: [
        {
          name: 'Twitter',
          link: 'https://twitter.com/w_zce'
        },
        {
          name: 'About',
          link: 'https://weibo.com/zceme'
        },
        {
          name: 'divider'
        },
        {
          name: 'About',
          link: 'https://github.com/zce'
        }
      ]
    }
  ],
  pkg: require('./package.json'),
  date: new 
  Date()
}

const page = () => {
    return src('src/**/*.html', { base: 'src'})
    .pipe(plugins.swig({ data }))
    .pipe(dest('temp'))
}
```
yarn gulp page



##### 组合任务
因为三者没有任何牵连所以使用并行任务
引入parallel模块
```js
const { src, dest, parallel, series, watch } = require('gulp')
const compile = parallel(style, script, page)

module.exports = {
    compile
}
```
yarn gulp compile 完成并行组合任务


##### 图片和字体文件的处理
安装依赖: yarn add gulp-imagemin --dev(c++模块下载二进制集会比较慢）
这个插件可以处理图片和字体文件
```js
const imagemin = require('gulp-imagemin')
const image = () => {
    return src('src/assets/images/**', { base: 'src'})
    .pipe(imagemin())
    .pipe(dest('temp'))
}

const font = () => {
    return src('src/assets/fonts/**', { base: 'src'})
    .pipe(imagemin())
    .pipe(dest('temp'))
}

module.exports = {
    image,
    font,
}
```
yarn gulp image
yarn gulp font


##### 其他文件以及文件清除
其他文件就直接拷贝, 如不需要压缩的public文件
```js
const pub = () => {
    return src('public/**', { base: 'public'})
    .pipe(dest('temp'))
}
```

清除原有的temp文件
安装依赖: yarn add del --dev(他不是gulp下的插件, 只不过是可以使用, 是个promise方法
```js
const del = require('del')

const clean = () => {
    return del(['temp'])
}
```
然后和build串行, 确保先删除后添加temp文件
```js
const build = series(clean, parallel(compile, pub))
```


##### 自动加载插件
通过插件自动加载插件（禁止套娃喂）
安装依赖: yarn add gulp-load-plugins --dev
```js

// const sass = require('gulp-sass')
// const babel = require('gulp-babel')
// const swig = require('gulp-swig')
// const imagemin = require('gulp-imagemin')

const loadPlugins = require('gulp-load-plugins')
const plugins = loadPlugins()
```
此时我们已经不需要引入一些插件了, 将下面的插件名都更换为plugins.xxx 如
```js
.pipe(plugins.swig())
```


##### 热更新开发服务器
热更新开发服务器用于去在开发阶段调试应用, 我们可以通过gulp去启动和管理这个服务器, 实现在代码修改过后能自动编译, 刷新页面, 这样可以大大效率
这个模块给我提供了一个服务器, 功能比较强大, 支持我们修改代码过后自动更新到浏览器查看效果, 它不是gulp的插件, 只不过是通过gulp管理而已

安装依赖: yarn add browser-sync --dev
```js
const browserSync = require('browser-sync')
const bs = browserSync.create()
```
创建一个服务器
```js
onst serve = () => {
  bs.init({
    notify: false, //控制浏览器刚打开时 右上角的小提示
    port: 2080, //端口
    // open: false,  //false不自动打开浏览器
    // files: 'dist/**',  //字符串 被监听的文件路径
    server: {
      baseDir: ['temp', 'src', 'public'],
      routes: {
        '/node_modules': 'node_modules' //配置路由便于查找html中没有转换的地址
      }
    }
  })
}

```
yarn gulp serve


##### 监听src变化以及构建过程优化
引入watch模块
```js
const { src, dest, parallel, series, watch } = require('gulp')

const serve = () => {
  watch('src/assets/styles/*.scss', style) //监听, 有变化就执行后面的任务
  watch('src/assets/scripts/*.js', script)
  watch('src/*.html', page)
  watch('src/assets/images/**', image)
  watch('src/assets/fonts/**', font)
  watch('public/**', extra)

  bs.init({
    notify: false,
    port: 2080,
    server: {
      baseDir: ['temp', 'src', 'public'],
      routes: {
        '/node_modules': 'node_modules'
      }
    }
  })
}

```
yarn gulp serve
> 这里可能会因为swig模板引擎缓存的机制导致页面不会变化
此时需要额外将swig 选项中的cache设置为false
具体参考源代码72行

##### 注意
图片字体和不变的文件, 在开发阶段不需要一直重构, 会影响效率, 同事删除并行任务中的图片和字体
```js
//   watch('src/assets/images/**', image)
//   watch('src/assets/fonts/**', font)
//   watch('public/**', extra)
```

但是如果图片字体文件发生变化也要监视到
```js
  watch([
    'src/assets/images/**',
    'src/assets/fonts/**',
    'public/**'
  ], bs.reload)
```
如果给pipe加上reload, 就不需要bs中的files监视了, 如:
```js
const page = () => {
    return src('src/**/*.html', { base: 'src'})
    .pipe(plugins.swig({ data }))
    .pipe(dest('temp'))
    .pipe(bs.reload({ stream: true }))
}
```


##### useref文件引用处理
会自动创建构建注释来处理, 会将路径中的文件打包到某文件中
安装依赖: yarn add gulp-useref --dev
这里的src接口针对的是temp路径下的文件
```js
const useref = () => {
  return src('temp/*.html', { base: 'temp' })
    .pipe(plugins.useref({ searchPath: ['temp', '.'] }))
    // html js css
    .pipe(plugins.if(/\.js$/, plugins.uglify()))
    .pipe(plugins.if(/\.css$/, plugins.cleanCss()))
    .pipe(plugins.if(/\.html$/, plugins.htmlmin({
      collapseWhitespace: true,
      minifyCSS: true,
      minifyJS: true
    })))
    .pipe(dest('dist'))
}
```
然后对html css js进行压缩
安装依赖: yarn add gulp-htmlmin gulp-uglify gulp-clean-css --dev
```js
const useref = () => {
  return src('temp/*.html', { base: 'temp' })
    .pipe(plugins.useref({ searchPath: ['temp', '.'] }))
    .pipe(dest('dist'))
}
```
判断是什么文件, 来决定后续的操作
安装判断依赖: yarn add gulp-if --dev
```js
    .pipe(plugins.if(/\.js$/, plugins.uglify()))
    .pipe(plugins.if(/\.css$/, plugins.cleanCss()))
    .pipe(plugins.if(/\.html$/, plugins.htmlmin({
      collapseWhitespace: true,
      minifyCSS: true,
      minifyJS: true
    }))) //这里是处理html的空白字符 还可以删除空属性 移除注释等 需要自己扩展
```

> 如果文件中没有注释, 就useref不成功, 因为useref过后会删除注释，所以第二次需要注意, 先compile再useref, 输入流和输出流不应在同一个目录下

因为useref需要转换到别的文件夹  所以前面所有的转换（除字体图片等）先转换到temp 再useref到dist
```js
const build = series(clean, parallel(series(compile, useref), extra, image, font))
```

##### 补充
构建任务完成后, 需要好好写导出, 让人理解
需要放在package.json的scripts里
需要在.gitignore中忽略temp dist文件




#### 封装gulp
提取一个可复用的自动化构建工作流, 就是创建一个模块, 发布到仓库, 再使用就可以了
<!-- yarn global add zce-cli
zce init nm zce-pages
cd zce-pages
创建一个git仓库
新建一个文件（可以使用脚手架）
git init
git remote add origin https://xxxx.git
git push -->

这里就使用我们刚刚建立的简单的gulp

新建一个项目/仓库
提取gulp
code . -a(扩展一下）
将上面项目的gulpfile中的内容复制给index.js入口文件
将原项目中一大串依赖复制过来package.json中的dependencies中, 然后执行 yarn（这个是install）

回到原项目中把gulpfile清空, 删除node-modules 和 package中的devDepandencies, 这时他就是一个干净的项目了
执行 yarn link 和 yarn link "你的模块名字", 在 gulpfile 中写入导入文件
```js
module.exports = require('你的模块名字')
```
执行 yarn (安装生产依赖）, 安装gulp-cli gulp, 并启动 yarn build, 开始排错

**提取index中的data**
模块中新建pages.config.js来抽象一些不该被集成的东西, 如原项目中的data, 把数据放在这里面这个文件中
模块中修改
```js
let config = {
  // default config
}
try {
  const loadConfig = require(`${cwd}/pages.config.js`)
  config = Object.assign({}, config, loadConfig)
} catch (e) {}
    //使用data的地方也要修改
    .pipe(plugins.swig({ data: config.data, defaults: { cache: false } }))
    //找不到依赖时也可以这样引入
    .pipe(plugins.babel({ presets: [require('@babel/preset-env')] }))

```
yarn build可以成功, 基本完成


**抽象路径配置**
```js
let config = {
  // default config
  //这个build也要复制到配置文件pages.config.js一份
  build: {
    src: 'src',
    dist: 'dist',
    temp: 'temp',
    public: 'public',
    paths: {
      styles: 'assets/styles/*.scss',
      scripts: 'assets/scripts/*.js',
      pages: '*.html',
      images: 'assets/images/**',
      fonts: 'assets/fonts/**'
    }
  }
}
    //下面的路径全都修改为这种
    .pipe(dest(config.build.temp))

```


包装gulp cli, 就可以删除gulpfile了

在bin下的js中写入这些是为了提取命令行中的很长的重复部分, 这样提取之后就可以直接使用 '你的cli名' build来启动了
```js
#!/usr/bin/env node

process.argv.push('--cwd')
process.argv.push(process.cwd())
process.argv.push('--gulpfile')
process.argv.push(require.resolve('..'))

require('gulp/bin/gulp')
```
要在packsge.json中记得引入这个文件作为cli的入口文件: 
```js
  "bin": "bin/你的文件名.js",
  "files": [
    "lib",
    "bin"
  ],
```

**发布并使用模块**
发布
首先使用git提交代码, 
执行yarn publish 时, 淘宝镜像源是只读的所以publish不上去, 可以使用原镜像
>yarn publish --registryhttps://registry.yarnpkg.com

使用
先新建一个空项目
yarn add zce-pages --dev

yarn zce-pages build, 可以看到package中已有这些cli
```js
  "scripts": {
    "clean": "zce-pages clean",
    "build": "zce-pages build",
    "develop": "zce-pages develop"
  },
```
> 注: 官方镜像同步到淘宝镜像是有时间差的, 不要publish完马上就去下载, 可以先去官网查看版本


#### 写在最后

原创博文, 如有错误, 敬请指导!