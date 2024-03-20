---
title: TypeScript
date: 2021-05-28 12:42:30
categories: 
- 高级前端笔记
tags: 
- TypeScript
- 类
- 数据类型
- Flow
---
#### TypeScript
TypeScript 是基于 JS 之上的编程语言，解决了 JS 语言自有的类型系统的不足，大大提高代码的可靠程度。
TypeScript 是 JavaScript 自有类型系统的问题最终极解决方案。
TypeScript 是 JavaScript 的一个超集，支持 ECMAScript 6 标准。
TypeScript 设计目标是开发大型应用，它可以编译成纯 JavaScript，编译出来的 JavaScript 可以运行在任何浏览器上。

内容概要
- 强类型与弱类型
- 静态类型与动态类型
- JavaScript自有类型系统的问题
- Flow静态类型检查方案
- TypeScript语言规范与基本应用
- 类

#### 类型系统
**强类型与弱类型**（类型安全）
强类型在语言层面限制函数的实参类型必须与形参类型相同
弱类型语言层面不会限制实参的类型
强类型有更强的类型约束,而弱类型中几乎没有什么约束
强类型语言不允许任意的数据隐式类型转换
弱类型语言则允许任意的数据隐式类型转换
逻辑判断时就会抛出错误
python是强类型语言，但语法可以进行隐式类型转换

**静态类型与动态类型**（类型检查）
静态：一个变量声明时它的类型就是明确的，声明过后,它的类型就不允许再修改
动态：运行阶段才能够明确变量的类型，变量的类型也可以随时发生变化（变量是没有类型的，变量存的值是有类型的）

![](1622449196478.jpg)

js是弱类型, 动态类型, 确实了类型系统的可靠性
最早的js非常简单, 是脚本语言（不需要编译, 所以不会检测类型）可直接运行

> js是弱类型引发的的一些问题

1.异常需要等到运行时才能发现, 错误代码到运行时才会报错
```js
const obj = {}
// obj.foo()
setTimeout(() => {
  obj.foo()
}, 1000000)
// 不数到1000000时不会报错
```

2.函数功能可能发生改变
```js
function sum (a, b) {
  return a + b
}
console.log(sum(100, 100)) // 结果为数字
console.log(sum(100, '100')) //结果为字符串
```

3.对象索引器的错误用法
```js
const obj = {}
obj[true] = 100 // 属性名会自动转换为字符串
console.log(obj['true'])
```

> 强类型比较可靠可以规避大部分类型异常的错误

1.强类型代码错误更早暴露

2.强类型代码更智能，编码更准确 (编辑器无法推断类型，所以不能准确的推送提示）
```js
function render (element) {
  element.className = 'container'
  element.innerHtml = 'hello world'
}
```

3.重构更可靠
```js
const util = {
  aaa: () => {
    console.log('util func')
  }
}
```

4.减少了代码层面的不必要的类型判断
```js
function sum (a, b) {
  if (typeof a !== 'number' || typeof b !== 'number') {
    throw new TypeError('arguments must be a number')
  }

  return a + b
}
```

#### Flow
Flow 是 JS 的类型检查器（形参类型注解）
```js
function sum (a: number, b: number
{
return a + b
}
```

> flow安装流程

- yarn add flow-bin (node-modules/.bin下的插件可以直接运行)
- //@flow (需要在文件的最前面加上注解)

> flow一些简单的命令

- yarn flow init
- yarn flow
- yarn flow stop

**编译**
编译时去除注解
1.
yarn add flow-remove-types --dev
yarn flow-remove-types src -d dist

2.
yarn add ababel/core ababel/cli ababel/preset-flow --dev
创建并在.babelIrc中写
```js
{
"presets" : ["ababel/preset-flow"]
}
```
yarn babel src -d dist

**flow插件**
Flow Language Support

**flow类型推断**
虽然可以推断 但是还是建议添加注解

**类型注解**
函数 声明都可以注解


#### 数据类型
**6种原始类型**
```js
const a: string = 'foobar '
const b: number = Infinity /NaN/100
const c: boolean = false true
const d: null = null
const e: void = undefined
const f: symbol = SymbolO
```
**数组类型**
```js
const arr1: Array<number> =[1，2，3]
const arr2 : number[] =[1，2，3]
// 如果这个数组里面既存数字又存字符串，如何写
const arr1: (number | string)[] = [1, '2' ,3];

//元组
/**
* 如果说后面的这个数组，长度肯定只有三个，
* 第一个肯定是字符串，第二个肯定是字符串，第三个肯定是数字
* 这个时候第一个改成数字不会报错，数组这种类型约束已经约束不到了
* 这个时候元祖的作用就显现了
*/
const foo1: [string, string, number] = ['zina', 'girl', 18];
const foo2: [string,number] = [ 'foo',100]
```

**对象类型**
```js
const obj1: { foo: string, bar: number } = { foo: 'string', bar: 100 }
const obj2: { foo?: string, bar: number } = { bar: 100 }
const obj3: { [string]: string } = {}
obj3.key1 = 'value1'
obj3.key2 = 'value2'
```

**函数类型**
```js
function foo (callback: (string, number) => void) {
  callback('string', 100)
}
foo(function ( str,n){
    //str => string
    //n => number
})
//没有返回值 或者返回undefined
```

**特殊类型**
字面量类型
```js
const a: 'foo' = 'foo'
const type: 'success' | 'warning' | 'danger' = 'success'
```
混合类型
```js
type String0rNumber = string | number
const b: String0rNumber = 'string' // 100

```
maybe类型
```js
const gender: ?number = undefined
//相当于
const gender: number | null | void = undefined

```

**mixed类型**
所有类型（强类型）（要先明确类型才能使用不报错）
```js
function passMixed (value: mixed) {
  if (typeof value === 'string') {
    value.substr(1)
  }

  if (typeof value === 'number') {
    value * value
  }
}

passMixed('string')

passMixed(100)
```

**any类型**
所有类型（弱类型）（主要是为了兼容老代码）
```js
function passAny (value: any) {
  value.substr(1)

  value * value
}

passAny('string')

passAny(100)
```

**类型小结**
文档：https://flow.org/en/docs/types/
第三方类型手册：https://www.saltycrane.com/cheat-sheets/flow-type/latest/

**运行环境api**
内置方法


#### TypeScript

Javascript是基于ES6标准进行实现和扩展的
TypeScript可以编译出纯净,简洁的JavaScript代码, 并且可以运行在任何浏览器上
所以TypeScript是一种由微软开发的自由和开源的编程语言
它是JavaScript的一个超集, 而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程, 是一种给JavaScript添加特性的语言扩展

优点: ts的功能强大,生态健全,语言更完善,任何一种js运行环境都支持.
缺点: 语言本身多了很多概念, 不过因为是渐进式很容易上手. 项目初期使用ts还会增加一些成本, 所以更多是用在一些大项目中.

ng, vue3.0已经开始使用

**快速上手**
- yarn init --yes
- yarn add typescript --dev
- 新建ts文件 (完全可以使用js的标准语法使用)
- 编译 yarn tsc filename.ts
- 类型系统和flow是基本相同的
- ts不用编译就可以看到错误提示
- 编译时检查类型使用异常 移除注解 之类的语法 并转换es6的新特性

**配置文件**
- yarn tsc --init生成tsconfig.json
- 里面有些常用的方法
- yarn tsc 编译

**原始类型**
类型系统和flow是基本相同的
```js
const a: string = 'foobar'
const b: number = 100 // NaN Infinity
const c: boolean = true // false

// 在非严格模式（strictNullChecks）下，
// string, number, boolean 都可以为空
// const d: string = null
// const d: number = null
// const d: boolean = null

const e: void = undefined
const f: null = null
const g: undefined = undefined

// Symbol 是 ES2015 标准中定义的成员，
// 使用它的前提是必须确保有对应的 ES2015 标准库引用
// 也就是 tsconfig.json 中的 lib 选项必须包含 ES2015
const h: symbol = Symbol()
```

**标准库声明**
标准库就是内置对象所对应的声明

**中文错误消息**
yarn tsc --locale zh-CN
虽然但是, 不建议使用中文报错, 因为一旦有错误, 很难在引擎中搜索到有用的东西

**作用域问题**
默认文件中的成员会作为全局成员
多个文件中有相同成员就会出现冲突
```js
// 解决办法1: IIFE 提供独立作用域
(function () {
  const a = 123
})()

// 解决办法2: 在当前文件使用 export，也就是把当前文件变成一个模块
// 模块有单独的作用域
export {}
```

**object类型**
不单只对象, 而是指非原始类型（数组 函数 等）
```js
// object 类型是指除了原始类型以外的其它类型
const foo: object = function () {} // [] // {}

// 如果需要明确限制对象类型，则应该使用这种类型对象字面量的语法，或者是「接口」
const obj: { foo: number, bar: string } = { foo: 123, bar: 'string' }
```

**数组类型**
数组类型有两种表现形式
```js
const arr1: Array<number> = [1，2，3]
const arr2: number[] = [1，2，3]

// 如果是 JS，需要判断是不是每个成员都是数字
// 使用 TS，类型有保障，不用添加类型判断
function sum (...args: number[]) {
  return args.reduce((prev, current) => prev + current, 0)
}

sum(1, 2, 3) // => 6
```

**元组类型**
是一种特殊的数据结构，明确数组的数量和类型
上面已经解释过, 这里不做过多说明

**枚举类型Enum**
js没有这种结构, 如果需要用到, 一般是用对象来模拟
```js
enum PostStatus {
    Draft = 0,
    Unpublished = 1,
    Published = 2
}
```
数字枚举可以不给值, 或只给第一个元素值, 后续的元素会根据这个值自增
```js
enum PostStatus {
    Draft = 6,
    Unpublished, // => 7
    Published // => 8
}
```
字符串枚举不能自增, 要都给值, 但这种情况不常见

**函数类型**
严格的控制输入输出
加个问号或者给默认值可以变成可选参数, 但要放在最后面

**任意类型**
any不会进行类型检查, 不可靠, 尽量不用, 一般是兼容老代码时使用

**隐式类型推断**
如果没有添加类型, ts就会根据代码推断他的类型
如果无法推断就会被给any
建议为每个变量添加类型注解

**类型断言**
```js
// 假定这个 nums 来自一个明确的接口
const nums = [110, 120, 119, 112]
const res = nums.find(i => i > 0)
// const square = res * res

const num1 = res as number
const num2 = <number>res // JSX 下不能使用
```

**接口**
可以理解为规范或者契约
```js
interface Post {
  title: string
  content: string
}

function printPost (post: Post) {
  console.log(post.title)
  console.log(post.content)
}

printPost({
  title: 'Hello TypeScript',
  content: 'A javascript superset'
})
```

可选成员后加？
只读成员前加readonly
```js
interface Post {
  title: string
  content: string
  subtitle?: string
  readonly summary: string
}
const hello: Post = {
  title: 'Hello TypeScript',
  content: 'A javascript superset',
  summary: 'A javascript'
}

// hello.summary = 'other' 不可以
```

动态成员（不知具体的成员名称）
```js
interface Cache {
  [prop: string]: string
}

const cache: Cache = {}

cache.foo = 'value1'
cache.bar = 'value2'
```


#### 类
**基本使用**
- 描述一类具体事物的抽象特征
- 有子类, 父类
- 我们不能使用类, 只能使用类的实例
- ts增强了class的特性
- 类的属性必须要在使用之前声明

**访问修饰符**
- private是私有属性, 外部不能访问
- public是公有属性, 默认就是public属性
- protected是保护属性, 只允许在子类中访问
```js
public name: string // = 'init name'
private age: number
protected gender: boolean
```

**类的只读属性**
如果有访问修饰符，readonly只能放在修饰符的后面, 赋值后就不能再修改
```js
protected readonly gender: boolean
```

**类与接口**
- 不同类的共同属性可以用接口抽象出来
- 使用函数签名的方式来约束方法的运行
- implements实现接口
- 合理的是一个接口interface只约束一个能力
```js
interface Eat {
  eat (food: string): void
}

interface Run {
  run (distance: number): void
}

class Person implements Eat, Run {
  eat (food: string): void {
    console.log(`优雅的进餐: ${food}`)
  }

  run (distance: number) {
    console.log(`直立行走: ${distance}`)
  }
}

class Animal implements Eat, Run {
  eat (food: string): void {
    console.log(`呼噜呼噜的吃: ${food}`)
  }

  run (distance: number) {
    console.log(`爬行: ${distance}`)
  }
}
```

**抽象类**
- abstract抽象类可以具体实现, 接口不能实现
- 要定义抽象方法 不需要方法体
- 抽象类只能被继承 不能被实例
```js
abstract class Animal {
  eat (food: string): void {
    console.log(`呼噜呼噜的吃: ${food}`)
  }

  abstract run (distance: number): void
}

class Dog extends Animal {
  run(distance: number): void {
    console.log('四脚爬行', distance)
  }

}

const d = new Dog()
d.eat('嗯西马')
d.run(100)
```

**泛型**
我们在定义接口或类的时候没有指定具体的类型, 调用时才指定类型
```js
function createArray<T> (length: number, value: T): T[] {
  const arr = Array<T>(length).fill(value)
  return arr
}

// const res = createNumberArray(3, 100)
// res => [100, 100, 100]

const res = createArray<string>(3, 'foo')
```

**类型声明**
类型声明是为了兼容一些npm模块
文件中的.d.ts就是为了做类型声明模块的文件
如果又对应的类型声明模块, 就要安装对应的类型声明模块, 如果没有, 就要我们自己使用declare去声明

> 一个简单的类的实现
```js
class Person {
  public name: string // = 'init name'
  private age: number
  protected gender: boolean
  
  constructor (name: string, age: number) {
    this.name = name
    this.age = age
    this.gender = true
  }

  sayHi (msg: string): void {
    console.log(`I am ${this.name}, ${msg}`)
    console.log(this.age)
  }
}

class Student extends Person {
  private constructor (name: string, age: number) {
    super(name, age)
    console.log(this.gender)
  }

  static create (name: string, age: number) {
    return new Student(name, age)
  }
}

const tom = new Person('tom', 18)
console.log(tom.name)
// console.log(tom.age)
// console.log(tom.gender)

const jack = Student.create('jack', 18)
```


#### 写在最后
原创博文, 如有错误, 敬请指导!