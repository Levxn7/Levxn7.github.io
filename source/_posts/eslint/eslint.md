---
title: eslint
date: 2022-01-26 12:14:11
categories: 
- 前端进阶笔记
tags: 
- 规范化
- eslint
---
#### 规范化
为什么要有规范化标准
·软件开发需要多人协同
·不同开发者具有不同的编码习惯和喜好。
·不同的喜好增加项目维护成本
·每个项目或者团队需要明确统一的标准

#### eslint介绍
Lint 是检验代码格式工具的一个统称，具体的工具有 Jslint 、 Eslint 等, 最为主流的 Jslint 工具监测JS代码质量
ESLint很容易统一开发者的编码风格, 可以帮助开发者提升编码能力, 是基于node开发的npm模块, 现代化项目集成eslint, 会让人不拘泥于格式错误, 更专注于开发
首先我们初始化一个package
>npm install eslint --save-dev //带有cli命令
npx eslint --version //运行

--save-dev 会把 eslint 安装到 package.json 文件中的 devDependencies 属性中，意思是只是开发阶段用到这个包，上线时就不需要这个包了。

**快速上手**
> npx eslint --init //初始化一个配置文件, 如果要检查ts文件, 需要再问答时选择yes
npx eslint 路径 --fix //--fix会自动修复错误
 
一个简单的配置文件.eslintrc.js, 该文件导出一个对象，对象包含属性 env、extends、parserOptions、globals、rules 五个属性, 在根目录下创建 src/index.js 文件, 引入 const lint = 'eslint' 来检验代码是否符合规范
```js
module.exports = {
    env: {
        browser: false,
        es6: false
    },
    extends: [
        'standard'
    ],
    parserOptions: {
        ecmaVersion: 2015
    },
    rules: {
        'no-alert': "error"
    },
    globals: {
        "jQuery": "readonly"
    }
}
```
env、parserOptions、plugins
我的程序里要用到 ES6 、React 、JSX 语法，这几个属性就是让 Eslint 能够检验到这些语法的

extends
值为 "eslint:recommended" 的 extends 属性启用一系列核心规则，这些规则是经过前人验证的最佳实践（所谓最佳实践，就是大家伙都觉得应该遵循的编码规范），想知道最佳实践具体有哪些编码规范，可以在 eslint规则表 中查看被标记为 √ 的规则项。
关于 "airbnb" 编码规范说两句，在接触到大多数开源项目中，大多数的作者都会使用 "airbnb" 编码规范而不是 官方的 "extends": "eslint:recommended" 编码规范。
如果我们觉得 eslint-config-airbnb 规则配置中个别规则并不符合当前项目的要求，可以直接在 .eslintrc.js 配置 rules 属性，优先级高于共享规则 airbnb

ESLint 附带有大量的规则，修改规则应遵循如下要求：
"off" 或 0 - 关闭规则
"warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
"error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)
有的规则没有属性，只需控制是开启还是关闭，像这样："eqeqeq": "off"，有的规则有自己的属性，使用起来像这样："quotes": ["error", "double"]，具体有没有自带属性，可查看 eslint规则表。

几个常用的规则
```js
"quotes": [1, "single"],            # 单引号
"quote-props":[2, "as-needed"],     # 双引号自动变单引号
"semi": [2, "never"],               # 一行结尾不要写分号
"comma-dangle": [1,"always-multiline"]  # 对象或数组多行写法时，最后一个值加逗号
```

**配置注释**
注释规则参考: <http://eslint.cn/docs/user-guide/configuring#configuring-rules>
```js
const str1 = "${name} is a coder" // eslint-disable-line no-template-curly-in-string
```
 
**结合自动化gulp**
集成之后，ESLint 一定会工作,  这样做会与项目统一，管理更加方便
```js
.pipe(plugins.eslint())
.pipe(plugins.eslint.format())
.pipe(plugins.eslint.failAfterErrorO)

```

**结合webpack**
前置工作
·安装对应模块
·安装eslint模块
·安装eslint-loader模块
·初始化.eslintrc.js配置文件

关于react引入却没使用的问题 npm install eslint-plugin-react


**stylelint的认识**
使用方法基本可以参考eslint, 二者几乎是一样的
> npm install stylelint -D //安装
npx stylelint ./index.css //运行

配置文件为.stylelintsrc.js, 需在文件中继承 standard (安装 npm install stylelint-config-standard)
```js
module.exports ={
    extends: "stylelint-config-standard"
}
```

检查sass需要安装 npm install stylelint-config-sass-guidelines -D, 然后继承
```js
module.exports ={
    extends: [
        "stylelint-config-standard"
        "stylelint-config-sass-guidelines"
        ]
}
```

**prettier的使用**
> npm install prettier -D //安装
npx prettier . --write

不要完全依赖工具来格式化, 自己要写出良好的代码


**git hooks**
它的工作机制就是个钩子

eslint 结合git hooks, 使用hushy模块来实现
> npm intsall hushy -D //安装

在package中添加一个husky对象
```js
"husky" : {" hooks" :{
    pre-commit" : "npm run test"
    }
}
```
安装 npm intsall lint-staged -D
```js
"scripts" :{
    "test" : "eslint ./index.js",
    "precommit" :"lint-staged"
}
"lint-staged" : {
    "*.js":[
        "eslint",
        "git add"
    ]
}
```


#### 写在最后
其中部分内容参考简书: <https://www.jianshu.com/p/ad1e46faaea2>

原创博文, 如有错误, 敬请指导!



