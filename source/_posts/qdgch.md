---
title: 自动化构建
date: 2021-06-07 12:46:57
tags:
---
#### 自动化构建
自动化就是指机器代替手工去完成工作，构建可以理解为转换，自动化构建呢就是把源代码转换成生产环境中可以运行的代码或程序。这样的转换过程就叫做自动化构建工作流
作用就是脱离运行环境兼容带来的问题，在开发阶段使用提高效率的语法、规范和标准

典型的应用场景: 一些不被浏览器直接支持的用法如esNext sass 模板引擎，这时自动化构建工具就派上用场了

常用的自动化构建工具
grunt: 最早的前端构建系统，插件生态非常完善，几乎可以完成任何事，速度慢，磁盘读写操作多
gulp: 基于内存实现，速度较快，可以同时执行多个任务，插件系统也很完善，最流行
FIS: 百度团队推出，捆绑套餐，大而全, 易上手

**自动化构建初体验**
使用sass控制样式
安装历来: yarn add sass --dev
它会装在.bin目录下
然后运行命令时代码从输入路径转到输出路径:
.\node_modules\.bin\sass scss/main.scss css/style.css
如果每次输入命令就很麻烦, 后人不知你如何操作, 所以使用npm scripts来包装命令(是实现自动化构建工作流的最简单的方式）
在package.json中添加scripts
```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css"
}，
```
然后yarn build(npm run build)就可以了

看如何操作
yarn add browser-sync --dev 用来启动一个测试服务器去运行项目
```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css"，
    "serve" : "browser-sync ." //（.是运行当前的目录）
}，
```
yarn serve  此时browser-sync会自动运行服务器并唤起浏览器
如果在browser-sync生成之前并没有生成样式, 此时browser工作的时候就没有样式文件
此时就可以使用scripts的钩子命令
```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css"，
    "preserve" : "yarn build", //顾名思义 会在serve之前执行
    "serve" : "browser-sync ." 
}，
```
添加--watch可以监听文件的改变，改写sass保存后，css就会改变
```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css --watch"，//添加watch
    "preserve" : "yarn build", 
    "serve" : "browser-sync ." 
}，
```
使用npm-run-all可以同步执行不阻塞
yarn add npm-run-all --dev
```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css --watch"，
    "serve" : "browser-sync ." 
    "start" : "run-p build serve " //这个模块使用start run-p来声明同时执行的步骤
}，
```
yarn start时 build和serve就被同时执行了

```js
"scripts" : {
    "build" : "sass scss/main.scss css/style.css --watch"，
    "serve" : "browser-sync . --files \css/*.css\"" //可以让browser-sync在启动过后监听文件的变化，网页就能自动刷新了
    "start" : "run-p build serve " 
}，
```


#### grunt基本使用
**创建**
yarn init --yes
yarn add grunt
code gruntfile.js
gruntfile.js是 Grunt 的入口文件, 用于定义一些需要 Grunt 自动执行的任务, 需要导出一个函数, 此函数接收一个 grunt 的对象类型的形参
grunt 对象中会提供一些创建任务时会用到的 API
```js
module.exports = grunt => {
  // grunt.initConfig() 用于为任务添加一些配置选项
  grunt.initConfig({
    // 键一般对应任务的名称
    // 值可以是任意类型的数据
    foo: {
      bar: 'baz'
    }
  })

  grunt.registerTask('foo', () => {
    // 任务中可以使用 grunt.config() 获取配置
    console.log(grunt.config('foo'))
    // 如果属性值是对象的话，config 中可以使用点的方式定位对象中属性的值
    console.log(grunt.config('foo.bar'))
  })

  grunt.registerTask('foo', 'a sample task', () => {
    console.log('hello grunt')
  })

  grunt.registerTask('bar', () => {
    console.log('other task')
  })

  // // default 是默认任务名称
  // // 通过 grunt 执行时可以省略
  // grunt.registerTask('default', () => {
  //   console.log('default task')
  // })

  // 第二个参数可以指定此任务的映射任务，
  // 这样执行 default 就相当于执行对应的任务
  // 这里映射的任务会按顺序依次执行，不会同步执行
  grunt.registerTask('default', ['foo', 'bar'])

  // 也可以在任务函数中执行其他任务
  grunt.registerTask('run-other', () => {
    // foo 和 bar 会在当前任务执行完成过后自动依次执行
    grunt.task.run('foo', 'bar')
    console.log('current task runing~')
  })

  // 默认 grunt 采用同步模式编码
  // 如果需要异步可以使用 this.async() 方法创建回调函数
  // grunt.registerTask('async-task', () => {
  //   setTimeout(() => {
  //     console.log('async task working~')
  //   }, 1000)
  // })

  // 由于函数体中需要使用 this，所以这里不能使用箭头函数
  grunt.registerTask('async-task', function () {
    const done = this.async()
    setTimeout(() => {
      console.log('async task working~')
      done()
    }, 1000)
  })
}

```
yarn grunt foo/bar (不写就是默认, 默认中的数组顺序执行)


**标记任务失败**
使用return false标记失败, 后面的任务就不会被执行了, 如果执行 yarn grunt default --force 会继续执行失败后面的任务, 
如果是异步的就使用done(false)来标记
```js
module.exports = grunt => {
  // 任务函数执行过程中如果返回 false
  // 则意味着此任务执行失败
  grunt.registerTask('bad', () => {
    console.log('bad working~')
    return false
  })

  grunt.registerTask('foo', () => {
    console.log('foo working~')
  })

  grunt.registerTask('bar', () => {
    console.log('bar working~')
  })

  // 如果一个任务列表中的某个任务执行失败
  // 则后续任务默认不会运行
  // 除非 grunt 运行时指定 --force 参数强制执行
  grunt.registerTask('default', ['foo', 'bad', 'bar'])

  // 异步函数中标记当前任务执行失败的方式是为回调函数指定一个 false 的实参
  grunt.registerTask('bad-async', function () {
    const done = this.async()
    setTimeout(() => {
      console.log('async task working~')
      done(false)
    }, 1000)
  })
}
```

**配置选项方法**
initConfig: 可以用这种方法配置压缩文件路径, 键与文件名称保持一致
```js
module.exports = grunt => {
  grunt.initConfig({
    build: {
      options: {
        msg: 'task options'
      },
      foo: {
        options: {
          msg: 'foo target options'
        }
      },
      bar: '456'
    }
  })

  grunt.registerMultiTask('build', function () {
    console.log(this.options())
  })
}
```


**多目标任务**
.registerMultiTask
需要initconfig一个和任务名同名的属性, 属性值一定是个对象, 对象中每个属性的名字就是目标名称
```js
module.exports = grunt => {
  // 多目标模式，可以让任务根据配置形成多个子任务
  grunt.initConfig({
    build: {
      foo: 100,
      bar: '456'
    }
  })

  grunt.registerMultiTask('build', function () {
    console.log(`task: build, target: ${this.target}, data: ${this.data}`)
  })
}
```
yarn grunt build
yarn grunt build:bar

options是一个方法, 可以拿配置选项, 如果写在属性里面会覆盖外层
```js
module.exports = grunt => {
  grunt.initConfig({
    build: {
      options: {
        msg: 'task options'
      },
      foo: {
        options: {
          msg: 'foo target options'
        }
      },
      bar: '456'
    }
  })

  grunt.registerMultiTask('build', function () {
    console.log(this.options())
  })
}
```

**插件的使用**
插件是grunt的核心, 大体是用npm安装, 在gruntfile里面加载
如清理插件: yarn add grunt-contrib-clean
```js
module.exports = grunt => {
  grunt.initConfig({
    //grunt的插件一般是以grunt-contrib-name命名的, 所以属性名是clean
    clean: {
      temp: 'temp/**' //temp可以指定具体，也可以通配符, 如temp/*.txt, temp/**
    }
  })

  grunt.loadNpmTasks('grunt-contrib-clean')
}
```

**常用插件**
1.sass css的转换
>yarn add grunt-sass sass --dev

2.babel ex新特性的转换
>yarn add grunt-babel @babel/core @babel/preset-env --dev

3.watch 监视
>yarn add grunt-contrib-watch --dev



#### gulp的基本使用
The streaming build system

**基本使用**
安装开发依赖 
```js
yarn init --yes
yarn add gulp --dev//(同时会安装gulp-cli,也就是说会在.bin下有个命令）
code gulpfile.js
```

gulp通过导出函数成员的方式去定义, 导出的函数都会作为 gulp 任务
```js
// gulp 的任务函数都是异步的
// 可以通过调用回调函数标识任务完成
exports.foo = done => {
  console.log('foo task working~')
  done() // 标识任务执行完成
}
```
yarn gulp foo

gulp4.0以前需要载入gulp模块, 然后使用gulp.task()方法注册任务, 不过这种方式已经不被推荐了
```js
const gulp = require('gulp')

gulp.task('bar', done => {
  console.log('bar task working~')
  done()
})
```
yarn gulp bar


**默认任务**
```js
// default 是默认任务
// 在运行是可以省略任务名参数
exports.default = done => {
  console.log('default task working~')
  done()
}
```
yarn gulp


**组合任务**
```js
//series会依次执行, parallel会并行执行
const { series, parallel } = require('gulp')

const task1 = done => {
  setTimeout(() => {
    console.log('task1 working~')
    done()
  }, 1000)
}

const task2 = done => {
  setTimeout(() => {
    console.log('task2 working~')
    done()
  }, 1000)  
}

const task3 = done => {
  setTimeout(() => {
    console.log('task3 working~')
    done()
  }, 1000)  
}

// 让多个任务按照顺序依次执行
exports.foo = series(task1, task2, task3)

// 让多个任务同时执行
exports.bar = parallel(task1, task2, task3)
```


**异步任务的三种方式**
1.错误优先的回调函数
```js
exports.callback = done => {
  console.log('callback task')
  done()
}

exports.callback_error = done => {
  console.log('callback task')
  done(new Error('task failed'))
}
```
2.promise
```js
exports.promise = () => {
  console.log('promise task')
  return Promise.resolve()
}

exports.promise_error = () => {
  console.log('promise task')
  return Promise.reject(new Error('task failed'))
}

const timeout = time => {
  return new Promise(resolve => {
    setTimeout(resolve, time)
  })
}
```
async await (是语法糖，实际上还是promise）
```js
exports.async = async () => {
  await timeout(1000)
  console.log('async task')
}
```
3.stream （是其他方式中最为常见的方式）
readStream  end的时候结束
```js
const fs = require('fs')

exports.stream = () => {
  const read = fs.createReadStream('yarn.lock')
  const write = fs.createWriteStream('a.txt')
  read.pipe(write)
  return read
}

exports.stream = done => {
  const read = fs.createReadStream('yarn.lock')
  const write = fs.createWriteStream('a.txt')
  read.pipe(write)
  read.on('end', () => {
    done()
  })
}
```


**构建过程核心工作原理**
通过api模拟构建过程, 这是直接写入
```js
const fs = require('fs')
const { Transform } = require('stream')

exports.default = () => {
  const readStream = fs.createReadStream('normalize.css')  // 文件读取流
  const writeStream = fs.createWriteStream('normalize.min.css')  // 文件写入流
  const transformStream = new Transform({  // 文件转换流
    // 核心转换过程
    transform: (chunk, encoding, callback) => {
      const input = chunk.toString()
      const output = input.replace(/\s+/g, '').replace(/\/\*.+?\*\//g, '') //input先把空白字符替换掉，再替换掉注释
      callback(null, output) //callback是错误优先的对象 没有错误时第一个参数传null
    }
  })

  return readStream
    .pipe(transformStream) // 转换
    .pipe(writeStream) // 写入
}
```
yarn gulp


**文件操作**
读写: API+插件的使用, src（）创建读取流, dest（）创建写入流
对css的压缩转换: 安装依赖: yarn add gulp-clean-css --dev
重命名: 安装依赖: yarn add gulp-rename --dev
```js
exports.default = () => {
  return src('src/*.css')
    .pipe(cleanCSS())
    .pipe(rename({ extname: '.min.css' }))
    .pipe(dest('dist'))
}
```
yarn gulp
以上使用pipe src读取流，再pipe dest写入流的操作就是我们使用gulp的常规操作




#### FIS基本使用
fis不如前两个用的多, 也很久没更新版本了, 它高度集成
```js
yarn global fis3 
fis3 release
fis3 release -d output
```

添加fis-config.js
```js
fis.match('*.{js,scss,png}', {
  release: '/assets/$0'
})

fis.match('**/*.scss', {
  rExt: '.css',
  parser: fis.plugin('node-sass'),
  optimizer: fis.plugin('clean-css')
})

fis.match('**/*.js', {
  parser: fis.plugin('babel-6.x'),
  optimizer: fis.plugin('uglify-js')
})
```
fis3 release -d output

编译与压缩
yarn global add fis-parser-node-sass



#### 写在最后

原创博文, 如有错误, 敬请指导!




