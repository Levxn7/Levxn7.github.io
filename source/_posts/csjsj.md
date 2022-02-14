---
title: 前端工程化和实现一个脚手架
date: 2021-06-04 10:31:18
categories: 
- 前端进阶笔记
tags: 
- 前端工程化
- 脚手架
---
#### 前端工程化
node是前端的工业革命
前端工程化是指遵循一定的标准和规范, 通过工具去提高效率降低成本的一种手段,
一切以提高效率、降低成本、质量保证为目的的手段都属于\[工程化], 一切重复的工作都应该被自动化
工程化不等于某个工具, 工程化的核心应该是对项目整体的一种规划或者架构, 工具只是实现其的一种手段



> 使用工程化的原因

想要使用ES6+新特性, 但是兼容有问题
想要使用 Less / Sass/ PostCSS增强 CSS的编程性, 但是运行环境不能直接支持
想要使用模块化的方式提高项目的可维护性, 但运行环境不能直接支持
部署上线前需要手动压缩代码及资源文件, 部署过程需要手动上传代码到服务器
多人协作开发, 无法硬性统一大家的代码风格, 从仓库中pull回来的代码质量无法保证
部分功能开发时需要等待后端服务接口提前完成


> 工程化可以解决的问题

传统语言或语法的弊端
无法使用模块化/组件化
重复的机械式工作
代码风格统一、质量保证
依赖后端服务接口支持
整体依赖后端项目


> 一些成熟的工程化集成

create-react-app
vue-cli
angular-cli
gatsby-cli


#### 脚手架工具
脚手架工具是帮我们自动创建基础文件的工具
脚手架的本质作用就是创建项目基础结构、提供项目规范和约定, 相同的组织结构, 相同的开发范式, 相同的模块依赖, 相同的工具配置, 相同的基础代码都应该被自动化创建

**内容概要:**
- 脚手架的作用
- 常用的脚手架工具
- 通用脚手架工具剖析
- 开发一款脚手架

**常用的脚手架工具**
根据信息创建对应的项目基础结构有create-react-app, vue-cli, angular-cli等
例如创建一个组件/模块所需要的文件就可以用到plop

##### Yeoman
The web's scaffolding tool for modern webapps
可以搭配不同的generator去创建任何类型项目, 可以使用其搭建自己的脚手架
缺点是过于通用, 不够专注

**基本使用**
先安装基础模块: node, npm 或 yarn, 以下举例中我们使用yarn
```js
yarn global add yo //全局安装Yeoman
yarn global add generator-node // 一个node模块
mkdir my-module
cd my-module
yo node //input something...
```
执行yo node后的命令行如下(做个例子)
```js
localhost:my-module zxl$ yo node
? ==========================================================================
We're constantly looking for ways to make yo better! 
May we anonymously report usage statistics to improve the tool over time? 
More info: https://github.com/yeoman/insight & http://yeoman.io
? Module Name my-module
(node:77281) [DEP0066] DeprecationWarning: OutgoingMessage.prototype._headers is deprecated
(Use `node --trace-deprecation ...` to show where the warning was created)
? The name above already exists on npm, choose another? No
? Description a test project
? Project homepage url https://github.com/Levxn7/yoNodeTest
? Author's Name zxl
? Author's Email levxn7@qq.com
? Author's Homepage https://levxn7.github.io/
? Package keywords (comma to split) module,node
? Send coverage reports to coveralls No
? Enter Node versions (comma separated) 
? GitHub username or organization Levxn7
? Which license do you want to use? Apache 2.0
```


**sub generator**
不是所有generator都有子集生成器
```js
yo node:cli //cli应用
yarn link //建立通道
```

**yeoman常规使用步骤**
1.明确你的需求;
2.找到合适的 Generator;
3.全局范围安装找到的 Generator;
4.通过Yo运行对应的Generator;
5.通过命令行交互填写选项;
6.生成你所需要的项目结构;

##### 基于Yeoman搭建自己的脚手架

**1.创建一个Generator模块**
名称必须是generator-<name>格式
```js
mkdir generator-sample
cd generator-sample
yarn init //创建package.json
yarn add yeoman-generator // 这个模块提供了生成器的一个基类, 这个基类当中提供了一些工具函数 让我们在使用时更加便捷
```
使用vscode打开项目, 创建generators/app/index.js, 此文件作为Generator的核心入口, 需要导出一个继承自Yeoman Generator的类型
Yeoman Generator 在工作时会自动调用我们在此类型中定义的一些生命周期方法, 我们在这些方法中可以通过调用父类提供的一些工具方法实现一些功能, 例如文件写入
```js
const Generator = require('yeoman-generator')

module.exports = class extends Generator {
  writing () {
    // Yeoman 自动在生成文件阶段调用此方法
    // 我们这里尝试往项目目录中写入文件
    this.fs.write(
      this.destinationPath('temp.txt'),
      Math.random().toString()
    )
  }
}
```
yarn link发布
```js
localhost:generator-sample zxl$ yarn link
yarn link v1.22.10
success Registered "generator-sample".
info You can now run `yarn link "generator-sample"` in the projects where you want to use this package and it will be used instead.
✨  Done in 0.03s.
```


**2.新建一个项目**
```js
mkdir my-pro
cd my-pro
yo sample
```
```js
localhost:my-pro zxl$ yo sample
   create temp.txt
```
![](QQ20210604-114517@2x.png)
这就创建好了, 可以看到我们使用yo sample为新文件夹创建了一个txt文件并写入了一个随机数, 这就是最基本的yeoman的开发过程,
下面我们再给他加上一点东西

**3.根据模板创建文件**
相对于手动创建每一个文件, 模板的方式大大提高了效率
在app文件夹下新建templates/foo.txt
```js
const Generator = require('yeoman-generator')

module.exports = class extends Generator {
  writing () {
    // Yeoman 自动在生成文件阶段调用此方法
    // 通过模板方式写入文件到目标目录

    // 模板文件路径
    const tmpl = this.templatePath('foo.txt')
    // 输出目标路径
    const output = this.destinationPath('foo.txt')
    // 模板数据上下文
    const context = { title: 'Hello zce~', success: false }

    this.fs.copyTpl(tmpl, output, context)
  }
}
```
在foo中使用<%= title %>模式来替换关键字, yo sample结果生成foo.txt 其中内容也被替换了context中的内容
![](QQ20210604-115410@2x.png)


下面我们使用prompting方法接受用户输入数据, 然后把获取的结果传入context
```js
const Generator = require('yeoman-generator')

module.exports = class extends Generator {
  prompting () {
    // Yeoman 在询问用户环节会自动调用此方法
    // 在此方法中可以调用父类的 prompt() 方法发出对用户的命令行询问
    return this.prompt([
      {
        type: 'input', //用户输入方式
        name: 'name',  //对应返回值的键
        message: 'Your project name', //给用户的提示
        default: this.appname // appname 为项目生成目录名称
      }
    ])
    .then(answers => {
      // answers => { name: 'user input value' }
      this.answers = answers
    })
  }
  writing () {
    // // 通过模板方式写入文件到目标目录
    // 模板文件路径
    const tmpl = this.templatePath('foo.txt')
    // 输出目标路径
    const output = this.destinationPath('foo.txt')
    // 模板数据上下文
    const context = this.answers // 这里为用户输入结果
    this.fs.copyTpl(tmpl, output, context)
  }
}
```
我在foo中只写了一句<%= name %>, yo sample 后产生结果如下
![](QQ20210604-120142@2x.png)

##### vue generator案例
具体操作步骤其实上面已经说完了, 先创建一个yeoman, 把项目目录粘贴到templates中, 替换标签值（name等）, 把文件名全放到writing中做循环, 然后运行yarn link, 就可以创建了, 参考代码如下:
```js
const Generator = require('yeoman-generator')

module.exports = class extends Generator {
  prompting () {
    return this.prompt([
      {
        type: 'input',
        name: 'name',
        message: 'Your project name',
        default: this.appname
      }
    ])
    .then(answers => {
      this.answers = answers
    })
  }

  writing () {
    // 把每一个文件都通过模板转换到目标路径

    const templates = [
      '.browserslistrc',
      '.editorconfig',
      '.env.development',
      '.env.production',
      '.eslintrc.js',
      '.gitignore',
      'babel.config.js',
      'package.json',
      'postcss.config.js',
      'README.md',
      'public/favicon.ico',
      'public/index.html',
      'src/App.vue',
      'src/main.js',
      'src/router.js',
      'src/assets/logo.png',
      'src/components/HelloWorld.vue',
      'src/store/actions.js',
      'src/store/getters.js',
      'src/store/index.js',
      'src/store/mutations.js',
      'src/store/state.js',
      'src/utils/request.js',
      'src/views/About.vue',
      'src/views/Home.vue'
    ]

    templates.forEach(item => {
      // item => 每个文件路径
      this.fs.copyTpl(
        this.templatePath(item),
        this.destinationPath(item),
        this.answers
      )
    })
  }
}
```

##### 发布generator
将代码托管到git（.gitignore忽略node_modules） 对git命令行不熟悉的同学可以先学习一下git
```js
git init
git status
git add
git commit
create一个github地址
git remote add origin https://xxxx.git
git push -u origin master
yarn publish
如果镜像有问题就yarn publish --registry=https://registry.yarnpkg.com
```

##### plop
一个小而美的脚手架工具

**基本使用**
- 将plop模块作为项目开发依赖安装  (yarn add plop --dev)
- 在项目根目录下创建一个plopfile.js文件
- 在plopfile.js文件中定义脚手架任务
- 编写用于生成特定类型文件的模板  (创建hbs模板)
- 通过Plop提供的CLI运行脚手架任务  (yarn plop component)

#### 脚手架的工作原理
脚手架就是一个node-cli应用
通过 yarn init 创建一个package.json文件, 在里面添加一个bin字段,用来指定一下这个cli应用的入口文件cli.js, 这个入口文件必须要写一个特定的文件头 #!/usr/bin/env node , 如果是 Linux 或者 macOS 系统下还需要修改此文件的读写权限为 755, 具体就是通过 chmod 755 cli.js 实现修改

与用户交互需要安装 inquire 模块,  yarn add inquire//对用户发起命令行交互询问
然后引入模块
```js
#!/usr/bin/env node

// Node CLI 应用入口文件必须要有这样的文件头
// 如果是 Linux 或者 macOS 系统下还需要修改此文件的读写权限为 755
// 具体就是通过 chmod 755 cli.js 实现修改

// 脚手架的工作过程：
// 1. 通过命令行交互询问用户问题
// 2. 根据用户回答的结果生成文件

const fs = require('fs')
const path = require('path')
const inquirer = require('inquirer')
const ejs = require('ejs')

inquirer.prompt([ //这个方法可以发起用户询问
  {
    type: 'input',
    name: 'name',
    message: 'Project name?'
  }
])
.then(anwsers => {  //这个方法获取用户回答
  // console.log(anwsers)
  // 根据用户回答的结果生成文件

  //新建一个templates模板, 通过 <%= name %> 模式获取用户输入的值

  // 模板目录
  const tmplDir = path.join(__dirname, 'templates')
  // 目标目录 使用node方法拿到执行命令行的目录
  const destDir = process.cwd()

  // 将模板下的文件全部转换到目标目录 这个方法自动扫描路径下的所有文件
  fs.readdir(tmplDir, (err, files) => {
    if (err) throw err
    files.forEach(file => {
        //拿到的file是相对路径
      // 通过模板引擎渲染文件 安装一个ejs的模板引擎 通过ejs.renderFile去渲染这个目录对应的文件
      ejs.renderFile(path.join(tmplDir, file), anwsers, (err, result) => {
        if (err) throw err

        // 将结果写入目标文件路径
        fs.writeFileSync(path.join(destDir, file), result)
      })
    })
  })
})
```
脚手架的原理虽然并不复杂, 但是他的意义是很大的, 他在项目创建环节大大提高了效率