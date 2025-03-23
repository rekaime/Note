# Js 冷知识

## 合并空值运算符

`??`

只有`null`和`undefined`才为不成立

```js
let val = null ?? 9
// 9

let val = undefined ?? 9
// 9

let val = false ?? 9
// false

let val = 0 ?? 9
// 0
```



## 包装 \_\_dirname & \_\_filename

```js
import path from 'path'
import { fileURLToPath } from 'url'

const __filename = fileURLToPath(import.meta.url)
const __dirname  = path.dirname(__filename)
```



# TypeScript

## 介绍

`Ts`是`Js`的扩展语言，包含`js`所有内容，是它的超集

增加了静态类型检查、接口、泛型等现代开发语言特性，适合大型项目

需要编译，生成`js`文件，交由浏览器或是其他`js`运行环境执行

`js`中存在一下几类问题：

+ 数据类型不明确
+ 逻辑存在漏洞时不报错
+ 可能访问不存在的属性
+ 拼写错误不提示

`ts`存在静态类型检查，减少运行时异常出现的概率



## 编译

### Ts Compiler

#### 安装

首先全局安装`TypeScript`

```shell
npm i typescript -g
```

#### 命令行编译

调用命令

```shell
tsc {{{filename.ts}}} # 后缀名可以省略
```

#### 自动化编译

创建`ts`编译控制文件

```shell
tsc --init
```

此时工程目录下会生成`tsconfig.json`配置文件，包含编译时的配置信息

其中默认编译生成`js`版本为`ES7|es2016`，可以调整为`ES6|es2015`

开启自动化转换，监视工程目录下`.ts`变化，如果只监视特定文件可以在命令后进行指定

```shell
tsc --watch # -w
```

调整：编译出错时不输出`.js`文件（配置信息也可以通过修改`tsconfig.json`的`noEmit`属性进行配置）

```shell
tsc --noEmit --watch
```



### rollup

#### 安装

```shell
npm i rollup typescript rollup-plugin-typescript2 @rollup/plugin-node-resolve
```

#### 配置

```shell
tsc --init
```

`rollup.config.js`

```js
import ts from 'rollup-plugin-typescript2'
import { nodeResolve } from '@rollup/plugin-node-resolve'
import path from 'path'
import { fileURLToPath } from 'url'

const __filename = fileURLToPath(import.meta.url)
const __dirname  = path.dirname(__filename)

export default {
    input: path.resolve(__dirname, './src/index.ts'),
    output: {
        file: path.resolve(__dirname, './dist/index.js'),
        format: 'iife', // 立即执行函数
        sourceMap: true // 调试用 tsconfig 里也要改这个
    },
    plugins: [
        nodeResolve({
            extensions: ['.ts', '.js']
        }),
        ts({
            tsconfig: path.resolve('./tsconfig.json'),
        })
    ]
}
```

运行

```shell
rollup -c -w
```

或通过`package.json`代理
```json
"scripts": {
    "dev": "rollup -c -w"
}
```



## tsconfig

### compilerOptions

#### Project

项目的配置



#### Language & Environment

+ target：编译结果的 Js 版本，同时会自动导入版本库的类型声明文件
+ lib：指定编译时引入的额外运行环境，同时引入类型声明，注释掉则会进行默认配置
+ jsx：指定 tsx 文件处理方式，默认`preserve`不编译，在`react`下会作为 React.createElement
+ experimentalDecorators：是否启用装饰器
+ emitDecoratorMetadata：使用装饰器时建议开启，记录元数据信息
+ jsxFactory：使用较少，为 preact 提供的
+ jsxFragmentFactory：使用较少，针对 fragment 实现不同的功能
+ jsxImportSource：更改导入路径，值为\<path>时编译结果生成`require('<path>/jsx-runtime')`
+ reactNamespace：更改命名空间，默认为`React`，值为\<namespace>时编译结果生成`<namespace>.createElement`
+ noLib：禁用的库文件
+ useDefineForClassFields：开启后，类的属性通过`Object.defineProperty`进行挂载
+ moduleDetection：值为 auto 会检查文件，不包含模块引入或导出语句则认为是全局模块；值为 force 则认定所有文件都是模块



#### Module

+ module：模块的规范
+ rootDir：指定项目编译源码的根路径，默认为项目根路径，tsc 会期望包含所有的 ts 文件，可以结合 exclude 字段使用
+ moduleResolution：模块解析方式
+ baseUrl：配置后指定模块导入的根目录，允许以绝对路径引入模块
+ paths：路径别名，如`{ "@/*": ["./src/types/*"] }`
+ rootDirs：将多个目录虚拟为同一个目录，也就是在其中一个目录下通过`./`引入另一个目录的文件是允许的，但是编译结果不会更改导入关系，却仍保留模块目录结构，通常只用于合并业务代码和 .d.ts 的关系
+ typeRoots：在指定路径下寻找类型声明文件，默认`[./node_modules/@types]`
+ types：如果开启，则代表 typeRoots 所有指定目录下哪些文件才需要引入
+ allowUmdGlobalAccess：是否允许使用 UMD 全局导出语法
+ moduleSuffixes：模块查找时，允许补充后缀进行查找
+ allowImportingTsExtensions：导入 mts 需要，还要同时配置 *moduleResolution* 和 *noEmit* 等，允许导入 Ts 扩展名
+ resolvePackageJsonExports：是否使用 package.json 的 exports 字段进行导入
+ resolvePackageJsonImports：是否使用 package.json 的 imports 字段进行导入
+ customConditions：配置额外的引入条件，例如配置`[<cond>]`，然后在对应包下 package.json 修改`export.default.<cond>`字段，mts 外用得不多
+ resolveJsonModule：允许导入 Json 文件
+ allowArbitraryExtensions：允许导入任意扩展名的文件，但前提是具备对应的类型声明文件
+ noResolve：禁用各种模块引入



#### JavaScript Support

+ allowJs：允许引入 Js
+ checkJs：在引入的 Js 中启用类型校验（校验是否标注）
+ maxNodeModuleJsDepth：在 node_modules 中查找 Js 文件的目录深度



####  Emit

+ declaration：是否基于 Ts 或 Js 代码生成 .d.ts 文件
+ declarationMap：为 .d.ts 文件生成映射文件 .d.ts.map，将模块引入目标更改为 Ts 文件
+ emitDeclarationOnly：只生成声明文件
+ sourceMap：为生成的 Js 文件附加映射文件
+ inlineSourceMap：生成的映射内容包含在 Js 文件内
+ noEmit：编译不生成文件
+ outFile：指定输出的文件，这个文件将所有的输出内容整合到自身，仅适用于 AMD 和 System 模块
+ outDir：指定输出的目录
+ removeComments：输出结果是否移除注释
+ importHelpers：允许项目从 tslib 一次性引入辅助函数，而不是在每个文件都引入一次（有时候低版本 Es 会为一些缺失语法补上一些函数作为补丁，但是会导致生成的代码中每个需要补丁的地方都生成一份冗余函数块）（使用这个配置项前需要`npm i tslib`，以引入一系列辅助方法）
+ downlevelIteration：代码降级后是否坚持使用迭代器（不是用则迭代器部分优化为循环，坚持采用则报错）
+ sourceRoot：调试时用，指定调试器查找资源文件的路径
+ mapRoot：调试时用，指定 sourcemap 的目录
+ inlineSources：是否在生成的 Js 代码中，将源码引入 sourcemap（防止代码被压缩无法找到映射）
+ emitBOM：Node 才使用到，是否在生成的文件开头生成 utf-8 编码的字节序列标记 BOM 头
+ newLine：指定换行符
+ stripInternal：Ts 使用类型注解标记了 @internal，则配置项控制对应内容编译后是否丢弃
+ noEmitHelpers：不引入 tslib 的方法，而是采取在需要的地方都生成一次 tslib 中的方法的方式
+ noEmitOnError：如果产生错误报告，不生成文件
+ preserveConstEnums：希望常量枚举也生成声明文件
+ declarationDir：指定生成的声明文件的目录



#### Interop Constraints

+ isolatedModules：是否确保模块转译安全性（表现在`export type { T } from 'module'`必须使用`export type`）
+ verbatimModuleSyntax：如果使用`import type`导入，则生成代码中不包含类型的导入（替代了 isolatedModules、preserveValueImports、importsNotUsedAsValues）
+ isolatedDeclarations：在导出时需要足够的注解，以便其它工具能快速生成声明文件
+ allowSyntheticDefaultImports：模块不包含默认导出时，是否允许默认导入行为，单独使用也行但是实际需要配合 esModuleInterop 配置项使用（目的在于解决 esm 和 cjs 的默认导入行为）
+ esModuleInterop：是否通过生成额外的 Js 代码以简化对 cjs 的导入，开启后如果没有人为配置 allowSyntheticDefaultImports 则默认开启它（编译结果会解析，如果不是 esm 则包装 mod = require() 的结果到对象`{ "default": mod }`中）
+ preserveSymlinks：是否禁止软链接转为真实路径
+ forceConsistentCasingInFileNames：是否强制确保模块引入时，模块名大小敏感



#### Type Checking

+ strict：是否启用严格模式，开启后后续以 strict 开头的配置项都会开启
+ noImplicitAny：是否在隐式 any 类型的表达式或声明文件处产生报错
+ strictNullChecks：是否启用 null & undefined 监测（使用变量及对应的方法前确保其不可能为空）
+ strictFunctionTypes：是否启用函数参数双向协变（检查函数参数和返回值的子类兼容性）
+ strictBindCallApply：是否保证`bind()`、`call()`和`apply()`的参数对原始函数的严格匹配
+ strictPropertyInitialization：是否开启对类实例属性的严格检测（检查声明而不初始化的行为）
+ strictBuiltinIteratorReturn：是否开启对内置迭代器的返回类型检测（说是这么说但是我不知道它有什么用）
+ noImplicitThis：是否开启对`this`的检查，当`this`类型为 any 时报错
+ useUnknownInCatchVariables：是否令 catch 分支的 e 默认为 unknown 而不是 any
+ alwaysStrict：是否保证生成的文件都带有`'use strict'`
+ noUnusedLocals：是否允许对未使用的变量报错
+ noUnusedParameters：是否允许对未使用的函数参数报错
+ exactOptionalPropertyTypes：是否将可选值解释为可写，而不是允许为 undefined（保证一个变量在初始化后不能再赋值为 undefined）
+ noImplicitReturns：是否开启返回值检查，保证函数每条分支都显式指定返回值
+ noFallthroughCasesInSwitch：是否开启 switch 检测，当分支没有 break 时报错（检查可能发生分支跳跃的情况）
+ noUncheckedIndexedAccess：是否开启索引访问检查，当通过索引进行访问时，为变量的可能类型添加`undefined`
+ noImplicitOverride：是否开启重写检查，确保子类重写方法时都必须显式使用 override 修饰
+ noPropertyAccessFromIndexSignature：是否开启索引签名访问检查，访问索引签名必须使用`[key]`的方式
+ allowUnusedLabels：是否允许存在未使用的标签（循环标签）
+ allowUnreachableCode：是否允许存在不可达代码



#### Completeness

+ skipDefaultLibCheck：是否跳过 Ts 文件中包含的 .d.ts 文件的检查（跳过类库的检查）
+ skipLibCheck：是否跳过所有 .d.ts 文件的检查（跳过 ts 内置类型检查）



### files

数组，指定打包入口文件，只能指定一系列单个文件

如果需要描述一系列文件可以使用 include 字段



### extends

指定一个额外的 tsconfig，默认配置项从它身上继承



### include

数组，指定需要打包的文件的匹配模式



### exclude

数组，指定打包时排除的文件的匹配模式



### references

可以引用其他的项目作为复合项，打包自身时会顺带打包对方



### watchOptions

用得不多



## 类型声明

类型一览：

+ `JavaScript`原生
  + `string`
  + `number`
  + `boolean`
  + `null`
  + `undefined`
  + `bigint`
  + `symbol`
  + `object`：`Array`、`Function`、`Date`、`Error`等
+ `TypeScript`新增
  + 新类型
    + `any`
    + `unknown`
    + `never`
    + `void`
    + `tuple`
    + `enum`
  + 新自定义类型的方式
    + `type`
    + `interface`

```ts
// 可以声明一些变量的同时确定类型
// 可以顺便进行初始化
let u // 具有隐式类型 any
let a: any
let s: string
let n: number = 1
let num = 1 // 如果进行初始化 ts 会推断它的类型 但是最好别这么做
let b: boolean = true
let f: Function
let o: Object = {}

// 函数可以指定变量以及输出的类型
// 直接声明的函数限制很严格 参数数量必须严格符合条件和数量
function fc(o1: number, o2: number): number {
    return o1 + o2
}

// 如果是后天赋值的函数 就没有输入输出的检查
f = (o1: number, o2: number): number => {
    return o1 + o2
}

// 剩余参数的运用
function add(pre: number, ...array: Array<number>): number {
    return array.reduce((pre, cur) => pre + cur, pre)
}

// 特殊用法 字面量类型 以后只能接收该字面量的值 可以理解为特殊的常量
let a1: 'rekaime'
let a2: 9 = 9
a1 = 'rekaime'
```

注意点：

包装类`Number`、`String`和`Boolean`都有对应的原始类型`number`、`string`、`boolean`，原始类型不能存储包装对象

在`ts`中建议类型设定为原始类型：

+ 原始类型是`js`简单数据类型，内存占用少，处理速度快
+ 包装对象是复杂类型，内存占用多
+ 自动装箱：`js`会在必要时自动将原始数据类型包装成对象，以便于访问相关的属性或方法

```ts
let s1: string // 基元
let s2: String // 包装类

s1 = 'rekaime' // 不报错
s2 = new String('rekaime') // 不报错

s1 = new String('rekaime') // 报错
s2 = 'rekaime' // 不报错
```



## 类型介绍

### any

别几把乱用，你喜欢`AnyScript`为什么不滚回去写`js`呢

表示放弃对变量的类型检查

`any`类型变量可以赋值给任意类型的变量

```ts
let a: any = 1
let s: string = 'rekaime'

s = a // 通过
```



### unknown

表示未知类型，相当于一个安全的`any`，你不能将`unknown`变量赋值给任何确定类型的变量

可以给不确定类型的数据定义类型

`unknown`会强制开发者在使用变量前检查类型，以提供安全性

```ts
let a: unknown = 1
let s: string = 'rekaime'

if (typeof a === 'string') s = a // 通过

// 断言
s = a as string
// or
s = <string>a
```

读取`any`的任何属性以及方法都不报错，而`unknown`与之相反

```ts
let s: unknown = 'rekaime'

s.length // 报错
s.toUpperCase() // 报错

// 通过断言解决问题 记得加分号
;(s as string).toUpperCase() // 通过
;(<string>s).length; // 通过
```



### never

表示任何值都不是

你别傻傻地用在变量上，没有任何意义

可以用来限定某些错误类方法的返回值

```ts
// 会报错 js函数默认返回 undefined
function fc():never { }

// 要么通过死循环组织函数退出 要么抛出错误阻止程序正常退出
function fc():never {
    throw new Error('Error!')
}
```

某些场景会将变量推断为`never`

```ts
let a: string = 'rekaime'

if (typeof a === 'string') {
    log('Im number!') // 一定能够到达这一分支
} else {
    log(a) // ts 认为该处的 a 是 never 因为没有任何值能到达这一分支
}
```



### void

表示不返回任何值，调用者也不能试图依赖返回值进行操作

一般用于函数返回值声明

函数的隐式返回值`undefined`不被`never`接受，但是`void`允许返回`undefined`（`return`是`return undefined`的简写形式）

```ts
function f(): undefined {
    log('rekaime')
}

function fc(): void {
    log('rekaime')
    // return undefined
}

let res
res = f()
if (!res) log('f pass') // 通过
res = fc()
if(!res) log('fc pass') // 报错
```



### object

#### object&Object

`Object`和`object`在实际开发中运用都较少，因为涵盖范围过大

`object`中能够存储**非原始类型**数据

```ts
let a: object
// 以下通过
a = {}
a = []
a = function() { }
a = new Number(1)
a = new String('rekaime')
a = new (class Person{})()
// 以下不通过
a = 1
a = 'rekaime'
a = true
a = null
a = undefined
```

`Object`存储可以调用`Object`方法的类型

```ts
let a: object
// 以下通过
a = {}
a = []
a = function() { }
a = new Number(1)
a = new String('rekaime')
a = new (class Person{})()

a = 1
a = 'rekaime'
a = true
// 以下不通过
a = null
a = undefined
```



#### 声明对象类型

可以限定一个变量类型为对象的同时，限定对象的属性

```ts
// 逗号分隔
let student1: { id: string, name: string, age?: number} // age 设为可选属性
// 分号分隔
let student2: { id: string; name: string; age?: number}
// 换行分隔
let student3: {
    id: string
    name: string
    age?: number
}
```



#### 索引签名

如果**声明对象类型**中有多个数量不确定的属性，可以通过索引签名批量处理

允许定义对象拥有任意数量的属性，这些属性键和类型是可变的

```ts
// key 可以自己改名 但是用 key 挺好的
let student: {
    id: string
    name: string
    [key: string]: any
}
```



#### 声明函数类型

可以提前声明一个变量为函数，同时指定变量的数量、类型，返回值的类型

```ts
let add: (o1: number, o2: number) => number // 这是 ts 语法 不是箭头函数
// OR
let add: {(o1: number, o2:number): number}

add = function(a, b) {
    return a + b
}
```

特殊情况

```ts
let f: () => void

f = function() {
    // 这种情况下 ts 不会严格要求函数返回空
    // 后面的 type 定义函数类型也存在相同情况 具体情况到时候说明
    return 9
}
```



#### 声明数组类型

```tsx
let array: string[]
let arr: Array<string>
```



### tuple

元组是一种特殊的数组类型，限定了一个数组的元素数量、一一对应的类型

```ts
let array: [string, number]
let arr: [string, number, boolean?] // 可选元素
let a: [string, ...number[]] // 可以有不限数量的 number
```



### enum

枚举用于定义一组命名常量，可以增强代码的可读性，便于维护

枚举类不能更改

#### 数字枚举

最常见的枚举类型，成员的值自动递增映射到数组下标上，且具有反向映射的特点

```ts
enum COLOR {
    RED,
    GREEN,
    BLUE
}
// 0: 'RED', 1: 'GREEN', 2: 'BLUE', RED: 0, GREEN: 1, BLUE: 2}

let c: COLOR
// 以下操作是允许的
c = COLOR.RED
c = 0 // 前提是枚举类中这个下标存在映射关系 你填个3马上报错
```

如果你闲着没事干，可以这么做

```ts
enum COLOR {
    RED = 3,
    GREEN = 6,
    BLUE
}
// {3: 'RED', 6: 'GREEN', 7: 'BLUE', RED: 3, GREEN: 6, BLUE: 7}
```



#### 字符串枚举

和数字枚举相比，丢失了反向映射的特性

```ts
enum COLOR {
    RED = 'red',
    GREEN = 'green',
    BLUE = 'blue'
}
// {RED: 'red', GREEN: 'green', BLUE: 'blue'}

let c: COLOR
c = COLOR.RED // 通过
c = 'red' // 报错
```



#### 常量枚举

一种特殊的枚举类型，使用`const`关键字定义，在编译时会被内联，避免产生额外代码

**关于编译时内联：**`ts`编译时，会将枚举成员引用替换为它们的实际值，而不是生成额外的枚举对象，可以减少`js`代码量，提高运行时的性能

比如数字枚举在创建时，就对应了`js`的一个数组，所有的枚举对象都在数组当中

而常量枚举不会在定义时在`js`中同步生成任何对象，当你使用到常量枚举时，`ts`会将常量枚举替换为对应的实际内容

`ts`

```ts
const enum COLOR {
    RED = 'red',
    GREEN = 'green',
    BLUE = 'blue'
}

let a: COLOR
a = COLOR.RED
```

`js`

```js
"use strict";
let a;
a = "red" /* COLOR.RED */;
```



### type

可以为任意类型创建别名，让代码更简洁、具有可读性，同时方便类型复用和扩展

#### type 使用

```ts
// 一般用法
type num = number
let a: num
```



### Class

#### 类的定义 - js

```js
// 定义类
class User {
    constructor(name, age) {
        this.name = name
        this.age = age
    }
    SayHi() {
        log(`Hi! Im ${this.name}. ${this.age} years old.`)
    }
}
const user = new User('rekaime', 18)
user.SayHi()

// 继承
class Student extends User {
    constructor(name, age, school) {
        super(name, age)
        this.school = school
    }
    SayHi() { // 重写方法
        log(`Hi! Im ${this.name}. ${this.age} years old. Learning in ${this.school}.`)
    }
}
const student = new Student('rekaime', 18, 'SCU')
student.SayHi()
```



#### 类的定义 - ts

```ts
// 定义类
class User {
    name: string
    age: number

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old.`)
    }
}

const user: User = new User('rekaime', 18)
user.SayHi()

// 继承
class Student extends User {
    school: string

    constructor(name: string, age: number, school: string) {
        super(name, age)
        this.school = school
    }
    override SayHi(): void { // 重写方法使用关键字 override 进行检查
        log(`Hi! Im ${this.name}. ${this.age} years old. Learning in ${this.school}.`)
    }
}

const student: Student = new Student('rekaime', 18, 'SCU')
student.SayHi()
```



#### 属性修饰符

+ `public`：可以被类内部、子类、类外部访问的属性

```ts
class User {
    public name: string // public
    public age: number // public

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old.`) // 能够访问
    }
}

class Student extends User {
    override SayHi(): void {
        log(`Hi! Im student ${this.name}. ${this.age} years old.`) // 能够访问
    }
}

const student: Student = new Student('rekaime', 18)
log(student.name, student.age) // 能够访问
student.SayHi()
```

+ `protected`：可以被类内部、子类访问的属性

```ts
class User {
    protected name: string // protected
    protected age: number // protected

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old.`) // 能够访问
    }
}

class Student extends User {
    override SayHi(): void {
        log(`Hi! Im student ${this.name}. ${this.age} years old.`) // 能够访问
    }
}

const student: Student = new Student('rekaime', 18)
log(student.name, student.age) // 禁止访问
student.SayHi()
```

+ `private`：只能被类内部访问的属性

```ts
class User {
    private name: string // private
    private age: number // private

    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }

    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old.`) // 能够访问
    }
}

class Student extends User {
    override SayHi(): void {
        log(`Hi! Im student ${this.name}. ${this.age} years old.`) // 禁止访问
    }
}

const student: Student = new Student('rekaime', 18)
log(student.name, student.age) // 禁止访问
student.SayHi()
```

+ `readonly`：只读属性

```ts
class User {
    public readonly id: string // 无法被修改
    public name: string
    public age: number

    constructor(id:string, name: string, age: number) {
        this.id = id
        this.name = name
        this.age = age
    }
}
```



#### 属性简写

```ts
class User {
    private readonly id: string
    public name: string
    public age: number

    constructor(id:string, name: string, age: number) {
        this.id = id
        this.name = name
        this.age = age
    }
}

// 简写后
class User {
    constructor(
        private readonly id: string,
        public name: string,
        public age: number
    ) { }
}
```



#### 抽象类

无法被实例化，只提供类的结构和行为，需要在派生类中进行实现

可以利用抽象方法定义格式，也可以拥有具体实现

```ts
abstract class User {
    constructor(
        private id: string,
        public name: string,
        public age: number
    ) { }
    // 一般方法
    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old.`)
    }
    // 抽象方法
    abstract Introduce(): void;
}

class Student extends User {
    constructor(
        id: string,
        name: string,
        age: number,
        public school: string // 派生类本身的属性要加上修饰符
    ) { super(id, name, age) }

    override SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old. Learning in ${this.school}.`)
    }
    // 抽象方法必须要进行重写
    override Introduce(): void {
        log('Im student')
    }
}
```



### interface

 定义对象的格式，只能规定对象具有哪些属性和方法，而不能有自身的具体实现

#### 类接口

```ts
interface UserInterface {
    id: string
    name: string
    age: number
    
    Introduce(): void
}

class Student implements UserInterface {
    constructor(
        public id: string,
        public name: string,
        public age: number
    ) { }

    Introduce(): void {
        log('Im student')
    }
}
```



#### 对象接口

```ts
interface UserInterface {
    id: string
    name: string
    age: number
    
    Introduce(): void
}

const user: UserInterface = { // 接口可以当做类型使用
    id: '1',
    name: 'rekaime',
    age: 18,

    Introduce() {
        log('Im user')
    }
}
```



#### 函数接口

```ts
interface AddInterface {
    (o1: number, o2: number): number
}

const add: AddInterface = function(o1, o2) {
    return o1 + o2
}
```



#### 接口间继承

```ts
interface UserInterface {
    id: string
    name: string
    age: number
    
    Introduce(): void
}

interface StudentInterface extends UserInterface {
    school: string
}

class Student implements StudentInterface {
    constructor(
        public id: string,
        public name: string,
        public age: number,
        public school: string
    ) { }

    SayHi(): void {
        log(`Hi! Im ${this.name}. ${this.age} years old. Learning in ${this.school}.`)
    }

    Introduce(): void {
        log('Im student')
    }
}
```



#### 接口自动合并 | 可重复定义

```ts
interface UserInterface {
    id: string
    name: string
    age: number
    
    Introduce(): void
}
// 出现了第二次接口定义 但只是会合并接口
interface UserInterface {
    gender: 0 | 1
}

class Student implements UserInterface {
    constructor(
        public id: string,
        public name: string,
        public age: number,
        public gender: 0 | 1,
        public school: string
    ) { }

    Introduce(): void {
        log('Im student')
    }
}
```



#### 和 type 的关联

相同点：

+ 都可以定义对象的结构，在很多时候可以互换使用

不同点：

+ `interface`：专注于对象和类，且支持继承、合并
+ `type`：可以定义类型别名和联合类型、交叉类型，不支持继承与合并



## 泛型

### 概念

在定义函数、类、接口时，无法确定类型，但是使用时可以明确，则可以利用泛型来代替未知类型



### 泛型函数

```ts
// 函数使用前声明泛型 T
function SayHi<T>(msg: T): void {
    log(msg)
}
SayHi<number>(1)

// 可以有多个泛型
function Log<T, U>(msg: T, err: U): void {
    Date.now() & 1 ? log(msg) : log(err)
}
Log<string, number>('OK', 404)
```



### 泛型接口

```ts
interface UserInterface<T> {
    id: string
    name: string
    age: number

    datalist: T[]
}

const user: UserInterface<number> = {
    id: '1',
    name: 'rekaime',
    age: 18,

    datalist: [1, 4, 5, 2, 3]
}
```



### 泛型类

```ts
class User<T> {
    constructor(
        public id: string,
        public name: string,
        public age: number,
        public datalist: T[]
    ) { }
}

const user: User<number> = new User('1', 'rekaime', 18, [1, 4, 5, 2, 3])
```



## 类型

### 类型级别

判断一个类型是否是另一个类型的子类（是否可以继承于另一个类型）

```ts
const subObj = { name: 'rekaime' }
type IT<T> = T extends subObj ? 'Yes' : "No"
```

级别（高到低）

+ `any`、`unknown`
+ `{}`、object、Object
+ 包装类：Number、String、Boolean
+ 基本类型：number、string、boolean、undefined、null、symbol、bigint
+ 字面量
+ `never`

注：

+ `{}`、object 和 Object 表达类型时几乎等价



特殊：

条件类型的分发机制

指的是将一个类型抽象为多个变量，例如 any 可以是 1 和不为 1 的变量之集

然后将集合的各个部分都拿出来比较一下，最终形成的结果取并集

```ts
type IT = any extends 1 ? true : false // boolean
```

```ts
// 一般情况
type IT = never extends 1 ? true : false // true

// never 在通过泛型传递时也有分发机制
// 只会返回 never
type IT<T> = T extends 1 ? true : false
type IType = IT<never> // never
```

```ts
// 分发出现的时机
// 联合类型通过泛型传递
// 在 extends 时进行分发
// extends 比较双方为裸类型

interface Fish {
    name: 'Fish'
}
interface Bird {
    name: 'Bird'
}
interface Water {
    name: 'Water'
}
interface Sky {
    name: 'Sky'
}
type GetType<T extends Fish | Bird> = T extends Fish ? Water : Sky

type IT = GetType<Fish & Bird> // Water
type IT = GetType<Fish | Brid> // Water | Sky

type GetType<T extends Fish | Bird> = [T] extends [Fish] ? Water : Sky
type IT = GetType<Fish | Brid> // Sky
```

消除分发

```ts
type NoDistribute<T> = T & {}
```



### 联合类型

`type`还可以处理联合类型

```ts
// 处理联合类型
type Code = number | string
type Gender = '男' | '女'
type School = {
    id: Code
    name: string
    addr: string
}

let user = {
    id: Code
    name: string
    gender: Gender
    school: School
}

user = {
     id: 1,
     name: 'rekaime',
     gender: '男',
     school: {
        id: 0,
        name: 'SCU',
        addr: 'SiChuan'
     }
}
```



### 交叉类型

指的是通过`&`将多个类型合并为同一个类型

```ts
type Code = number | string
type Gender = '男' | '女'
// 基本信息类型
type BaseInfo = {
    id: Code,
    name: string
}
// 处理联合类型
type School = BaseInfo & {
    addr: string
}
// 处理联合类型
let student: BaseInfo & {
    gender: Gender
    school: School
}

student = {
     id: 1,
     name: 'rekaime',
     gender: '男',
     school: {
        id: 0,
        name: 'SCU',
        addr: 'SiChuan'
     }
}
```

如果你这么做，变量类型会变成`never`

```ts
let a: number & string
```

冷芝士

```ts
type Code = number | string
type Area = {
    width: number
    height: number
}
type BaseInfo = {
    id: Code
    name: string
}
type O1 = {
    area: Area
}
type O2 = {
    area: BaseInfo
}

let user: O1 & O2 = { // 包含相同属性 area 的类型合并
    area: { // 此时的 area 类型为 Area & BaseInfo
        width: 1,
        height: 1,
        id: 0,
        name: 'rekaime'
    }
}
```

对于函数：

```ts
interface I1 {
    fn: (value: number) => string
}

interface I2 {
    fn: (value: string) => string
}

type T = A & B
// 等同于
interface T {
    fn: (value: number | string) => string
}
```





### 类型批处理

类型提取

```ts
const student = { name: 'rekaime', age: 18 }
const IStudent = typeof student
const IKeys = keyof student
```

类型合并

```ts
interface IName {
    name: string
}
interface IAge {
    age: number
}
type Compute<T extends object> = {
    [key in keyof T]: T[key]
}

type IT = Compute<IName, IAge> // { name: string, age:number }
```

类型可选

```ts
// 浅 对象第一级属性确实是可选
// 但是如果一级属性类型是对象 一旦填了二级属性则必须满足接口
type Partial<T extends object> = {
    [key in keyof T]?: T[key]
}

// 深 通过递归实现
type Partial<T extends object> = {
    [key in keyof T]?: T[key] extends object ? Partial<T[key]> : T[key]
}
```

类型必选（`-?`表示移除可选）

```ts
type Required<T extends object> = {
    [key in keyof T]-?: T[key]
}
```



### 内置类型

直接看源码

基于条件：`Extract`、`Exclude`

基于映射：`Partial`、`Required`、`Readonly`

基于结构：`Pick`、`Omit`、`Record`

基于推断：`InstanceType`、`ReturnType`、`Parameters`

```ts
type Exclude<T, U> = T extends U ? never : T
type Extract<T, U> = T extends U ? T : never
// 旧版实现 type NonNullalbe<T> = T extends null | undefined ? never : T
type NonNullalbe<T> = T & {}
```



### 模板字符串

```ts
const person = {
    name: 'rekaime',
    age: 30
}

type Person = typeof person
type RePerson = {
    [K in keyof Person as K extends string ? `re_${K}` : K]: Person[K]
}
```



### 关于函数返回值

```ts
type LogFunction = () => void
let f: LogFunction = function() {
    // 和前面声明函数类型一样 ts 不会严格限制返回值
    // 你可以返回你想返回的东西 但是你仍然不能尝试对返回结果进行操作
    return 9
}

// 这是为了避免以下代码无法执行
const src = [1, 4, 5]
const dest = [2, 3]
// 传入的回调函数返回值类型是 void
// 但如果你写成箭头函数 会 return src.push(el) 该语境下返回 el
dest.forEach(el => src.push(el))
```

通过`type`可以指定构造函数类型

```ts
// new     表示该类型可以用 new 操作符进行操作
// ...args 表示构造器支持任意数量的参数
// any[]   表示构造器支持任意类型的参数
// {}      表示返回的类型是对象

type Constructor = new (...args: any[]) => {}

// 声明目标是构造函数的同时 指定一些静态属性
type Constructor = {
    new (...args: any[]): {}
    
    id: string
    name: string
}

class User {
    static id: string
}
```



### 类型断言

联合类型`type1 | type2`在未赋值时，只能使用两种类型交叉的方法

因此可以通过断言确定使用过程中变量一定是某一种类型

```ts
let v: number | string
// 断言
(v as number).toFixed(2)
(v as string).toUpperCase()
(<string>v).toUpperCase()

// 非空断言 也就是一定存在值
v! as number
<number>v!
```

双重断言，会破坏原有的关系

```ts
v as unknown as boolean // 强制转为 boolean
```



### 拓展 - 非空断言

`?`是 Js 的取值断言，有则取值

`!`是 Ts 的非空断言，表示值一定存在

```ts
const ele = document.querySelector('div')  // Type = HTMLElement | null
ele.style.background  // 报错

const ele = document.querySelector('div')! // Type = HTMLElement
ele.style.background  // 通过

const ele = document.querySelector('div')  // Type = HTMLElement | null
ele!.style.background // 通过
```



### infer

因为 Js 函数默认不执行，而我们有时候希望获取函数返回值的类型

因此可以通过`infer`来推断返回值类型

```ts
// inference
// 这是一个内置类型的源码
type ReturnType<T extends (...args: any) => any> = T extends (...args: any[]) => infer R ? R : any

// function anyFunction() {}
// type IT = ReturnType<typeof anyFunction>

// 类似地还有以下内置类型
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never

type InstanceType<T extends abstract new (...args: any) => any> = T extends abstract new (...args: any) => infer R ? R : any;

```

利用模板字符串可以拆分字符串类型

```ts
type FirstChar<T extends string> = T extends `${infer L}${infer Second}${infer R}` ? L : T
```





### 兼容性

#### 概述

+ 函数：

  + 参数少 => 参数多的

  + 返回值小的 => 返回值大的

+ 类：

  + 结构大的 => 结构小的

  + 没有 private

+ 枚举：

  + 两个枚举之间不兼容

+ 泛型：

  + 生成的结果一致则兼容

+ 对象：

  + 属性多的可以给少的

+ 包含类型层级兼容性



#### 逆变 & 协变

逆变：函数参数标记子，可以传父

协变：函数返回值标记父，可以返回子

```ts
class Parent { lv1() {} }
class Child { lv2() {} }
class Grand { lv3() {} }

function fn(cb: (target: Child) => Child) {
    cb(new Child())
}

fn((target: Grand): Grand => {
    // 可以传父 保证回调提供的对象上具备应有的属性
    // 可以返回孙 保证回调执行完毕后 被操作的返回值具备应有的属性
    return new Child()
})
```

逆变的屏蔽

```ts
interface IT<T> {
    fn: () => T // 开启逆变
    fn(): T     // 关闭逆变
}
```



### 类型保护

在变量不确定类型时不贸然使用`any`，而是采用`unknown`

`unknown`类型数据需要先进行类型断言才能进行使用

常见类型保护方式：

+ typeof
+ instanceof
+ if
+ is 方法
+ `?`、`!`（断言）

关于 is 方法

```ts
interface Cat {
    meow: string
}
interface Dog {
    bark: string
}
type Animal = Cat | Dog

function isCat(animal: Animal): animal is Cat {
    return (animal as Cat).meow !== undefined
}
```





## 类型声明文件

### 概述

如果你直接通过`import`在`ts`文件中引入其他`js`文件导出的对象，会因为文件路径存在隐式类型`any`而报错

因此需要`.d.ts`文件进行声明

`.d.ts`主要作用是为现有的`js`代码提供类型信息，使`ts`能够在使用这些`js`库或模块时进行类型检查

`tool.js`

```js
export function add(o1, o2) {
    return o1 + o2
}
```

`tool.d.ts`

```ts
declare function add(o1: number, o2: number): number

export { add }
```

`index.ts`

```ts
import { add } from './tool.js'

log(add(2, 3))
```



### 声明

```ts
// env.d.ts
// 如果目标为 Ts 语法则可以不加 declare
declare class MyClass { }
declare interface MyInterface {}

declare module 'axios' {
    function axios(url, opts) {}
    
    export default axios
}

declare module '*.vue' {
    import type { DefineComponent } from 'vue'
    const comp: DefineComponent
    export default comp
}

declare module '*.jpg' {
  const src: string
  export default src
}
```



### 全局声明

一般`declare`编写在`.d.ts`文件中，但是如果希望在 Ts 文件中进行全局声明，可以通过以下方式

```ts
// my.ts
declare global {
    class MyClass { }
    interface MyInterface {}
}
```



### 包声明文件

第三方包的声明文件会通过`package.json`标注入口

有时需要我们手动下载声明文件，这些文件会一同存放在`@types`目录下

声明文件的寻找规则：

+ 首先看用户是否自己定义过对应的类型，包括`tsconfig.json`中指定的`types`，否则看`node_modules`中是否有定义声明文件
+ `node_modules`存在对应的包，则搜索`package.json`的`types`字段
  + 有则通过`types`作为入口
  + 无则试图寻找该包下的`index.d.ts`
+ `node_modules`不存在对应的包，则搜索`node_modules/@types`下同名文件夹
+ 以上方式都无法找到对应的声明文件，则报错



### 声明文件的引用

只能在最顶端使用，可以引用其他的声明文件

一般通过`interface`进行加工

```ts
/// <reference path="<path>" />
使用指定路径的声明文件

/// <reference types="<type>" />
使用 @types 下其他包的声明文件

/// <reference lib="lib.es6.promise" />
使用内置的声明文件
```





## 命名空间

对于模块化，可以将不同的业务逻辑分装在不同文件中

而命名空间可以在文件内隔离变量

```ts
export namespace N1 {
    class Student {}
}

export namespace N2 {
    class Student {}
}
// 使用时
import { N1, N2 } from 'module'

new N1.Student()
new N2.Student()
```



## 模块扩展语法

```ts
// 用于支持 CJS 和 UMD
// 是 Ts 特有的
export = React
// 等同于
module.exports = React

// Ts 的 UMD 模块导出扩展
// 允许模块同时以命名空间的形式出现在全局 不需要导入也可以使用
// 构建支持浏览器和 Node.js 的库
export as namespace React
```

使用：

```ts
declare namespace _ {
    // function withIn(): void
    
    interface Lodash {
        copy(): void
    }
}

declare const _: _.Lodash

export as namespace _
export = _
```



# 装饰器

## 概念

本质是一种特殊函数，可以对类、属性、方法、参数进行扩展，让代码更简洁

截至目前，装饰器依然是实验性特性，需要开发者手动调整配置，开启装饰器支持



## 配置

在`tsconfig.json`中，将`experimentalDecorators`更改为`true`



## 关于 target

指向：

+ 类本身：描述类、静态成员时
+ 原型：描述原型、实例属性方法时



## 类装饰器

虽然`Ts5.0`可以直接使用类装饰器，但是为确保其他装饰器可用，仍建议使用`experimentalDecorators`配置来开启装饰器支持

### 基本用法

```ts
// 定义一个函数作为装饰器
function Decorator(target: Function) {
    log(target)
}

// 在其他对象上方通过 @ 调用装饰器
// 此时相当于 Decorator(User)
@Decorator
class User {
    constructor(
        public id: string,
        public name: string,
        public age: number
    ) { }
}
```



### 应用

修改类的`toString()`方法

```ts
function toString(target: Function) {
    target.prototype.toString = function() {
        return JSON.stringify(this)
    }
}

@toString
class User {
    constructor(
        public id: string,
        public name: string,
        public age: number
    ) { }
}

const user = new User('1', 'rekaime', 18)
log(user.toString())
```



### 关于返回值

如果类装饰器**有**返回值，返回的是一个新的类，那么新的类会替换掉被装饰的类

如果类装饰器**无**返回值，那么被装饰的类不会被替换



### 高级功能实现

为类`User`添加一个新的属性，我们可以通过该属性查看对象的创建时间

原始类结构

```ts
class User {
    constructor(
        public name: string,
        public age: number
    ) { }

    SayHi() {
        log('Hi')
    }
}
```

通过装饰器添加属性

```ts
function LogTime<T extends Constructor>(target: T) {
    return class extends target {
        readonly createTime: Date
        constructor(...args: any[]) {
            super(...args)
            this.createTime = new Date()
        }
    }
}

@LogTime
class User {
    constructor(
        public name: string,
        public age: number
    ) {
        log(this)
    }

    SayHi() {
        log('Hi')
    }
}
```



## 装饰器工厂

是一个返回装饰器的函数，可以为装饰器添加参数，更灵活地控制装饰器的行为

```ts
// 通过装饰器工厂 实现 createTime 的可获取
function LogTime(readable: boolean) {
    // 返回装饰器
    return function<T extends Constructor>(target: T) {
        return class extends target {
            private readonly createTime: Date
            constructor(...args: any[]) {
                super(...args)
                this.createTime = new Date()
                if (readable) target.prototype.getCreateTime = function() {
                    return this.createTime
                }
            }
        }
    }
}

interface User {
    getCreateTime: () => Date
}

@LogTime(true)
class User {
    constructor(
        public name: string,
        public age: number
    ) {
        log(this)
    }

    SayHi() {
        log('Hi')
    }
}
```



## 方法装饰器

```ts
// 方法装饰器
function Enum(isEnum: boolean) {
    return function (target: any, prop: string, descriptor: PropertyDescriptor) {
        // target 对象 其中包含被装饰器修饰的方法的键值对
        // prop   当前装饰器指向的方法名
        // descriptor 属性修饰符 target[prop] === descriptor.value
        descriptor.enumerable = isEnum
        // return descriptor
    }
}

class Animal {
    @Enum(true)
    eat() {}
}
```

如果希望修改方法本身

```ts
function MethodDecoration() {
    return function (target: any, prop: string, descriptor: PropertyDescriptor) {
        const fn: Function = descriptor.value

        descriptor.value = function (...args: any[]) {
            // ...
            fn.call(this, ...args)
            // ...
        }

        return descriptor
    }
}
```



## 属性装饰器

因为 Es2015 和 EsNext 的编译结果不同，因此属性装饰器的效果也不同

```ts
function toUpperCase(target: any, props: string) {
    let value = ''

    Object.defineProperty(target, props, {
        get() {
            return value
        },
        set(v) {
            value = v.toUpperCase()
        }
    })
}

class Animal {
    @toUpperCase
    public name = 'cat'
}

// ES6
log(new Animal()) // 'CAT'

// ESNext
log(new Animal()) // 'cat'
```



## getter & setter 装饰器

不能向同名的 getter 和 setter 添加同一个装饰器

```ts
function toUpperCase(target: any, prop: string, descriptor: PropertyDescriptor) {
    const { get, set } = descriptor
    descriptor.set = function(value: string) {
        set?.call(this, `$ ${value}`)
    }
    // return descriptor
}

class Animal {
    private _name = ''
    constructor() {
        this.name = 'cat'
    }

    get name() {
        return this._name
    }
    @toUpperCase
    set name(value) {
        this._name = value
    }
}
const a = new Animal()
```



## 参数装饰器

```ts
function Params(target: any, prop: any, index: number) {
    // 在构造方法上则返回类
    // 在实例方法上返回对象 其中为被装饰器修饰的方法的键值对
    log(target)
    // 在构造方法上 === undefined
    // 在实例方法上返回方法名 target[prop]
    log(prop)
    // 该参数是第几个参数
    log(index)
}

class Animal {
    constructor(@Params name: string, @Params age: number) { }
    
    sleep(@Params voice: string) { log(`cat ${voice}`) }
}
```



## 装饰器运行顺序

遵循洋葱模型

所有参数装饰器优先于本方法装饰器

getter & setter => 实例属性方法 => 静态属性方法 => 类

```ts
function Echo(output: string) {
    return function(target: any, prop?: any, descriptor?: any) {
        log(output)
    }
}

@Echo('类装饰器')
class cls {
    
    constructor(@Echo('参数装饰器 - 构造函数') value: any) { }
    
    @Echo('静态属性装饰器')
    static type = 'Class'
    @Echo('静态方法装饰器')
    static getType(@Echo('参数装饰器 - 静态方法') value: any) { return this.type }

    @Echo('属性访问器装饰器')
    set value(@Echo('参数装饰器 - 属性访问器') value: any) { }
    @Echo('实例属性装饰器')
    private _value = 'value'
    @Echo('原型方法装饰器')
    getValue(@Echo('参数装饰器 - 原型方法') value: any) { return value }

}
```

```shell
参数装饰器 - 属性访问器
属性访问器装饰器
实例属性装饰器
参数装饰器 - 原型方法
原型方法装饰器
静态属性装饰器
参数装饰器 - 静态方法
静态方法装饰器
参数装饰器 - 构造函数
类装饰器
```



## 应用

实现设备的控制反转（ioc），帮助逻辑解耦

在下文在还可以结合反射元数据

```ts
class Container {
    public instances = new Map()
    public properties = new Map()
    // 负责注册设备
    bind<T>(deviceName: string, creator: () => T) {
        if (!this.instances.has(deviceName)) this.instances.set(deviceName, creator())

        return this.instances.get(deviceName)
    }
    // 从注册的设备中取出设备
    // 如果这个设备有依赖项则对依赖项进行绑定
    resolve<T>(deviceName: string): T {
        const instance = this.instances.get(deviceName)

        for (const property of this.properties) {
            const [key, ServiceKey] = property
            const [classKey, propKey] = key.split(':')

            if (instance.constructor.name !== classKey) continue
            const dep = this.resolve(ServiceKey)

            instance[propKey] = dep
        }
        return instance
    }
}

const container = new Container()
// 通过装饰器实施设备注册
function Provide(DeviceName: string) {
    return function (target: any) {
        container.bind(DeviceName ?? target.name, () => new target())
    }
}
// 通过装饰器实施依赖项绑定
function Inject(injectDeviceName: string) {
    return function (target: any, key: string) {
        container.properties.set(`${target.constructor.name}:${key}`, injectDeviceName)
    }
}

interface Monitor {
    display(): void
}

interface Host {
    start(): void
}

@Provide('Monitor23inch')
class Monitor23inch implements Monitor {
    display() {
        console.log('开启显示器')
    }
}

@Provide('XiaomiHost')
class XiaomiHost implements Host {
    start() {
        console.log('开启主机')
    }
}

@Provide('Computer')
class Computer {
    @Inject('Monitor23inch')
    public monitor!: Monitor23inch
    @Inject('XiaomiHost')
    public host!: XiaomiHost

    initialize() {
        this.monitor.display()
        this.host.start()
    }
}

const computer = container.resolve<Computer>('Computer')
computer.initialize()
```



# Reflect meta

## 概念

反射就是修改程序执行的行为

元数据就是描述数据的数据



## 引入

```shell
npm i reflect-metadata
```

```ts
import 'reflect-metadata'
```



## 基本使用

类似于创建 WeakMap

```ts
class Animal {
    static type = 'animal'
    sleep() { }
}

Reflect.defineMetadata('Class', 'class metadata', Animal)
Reflect.defineMetadata('Class Property', 'class property metadata', Animal, 'type')
Reflect.defineMetadata('Class Method', 'class method metadata', Animal, 'sleep')

Reflect.getMetadata('Class', Animal) // 'class metadata'
Reflect.getMetadata('Class Property', Animal, 'type') // 'class property metadata'
Reflect.getMetadata('Class Method', Animal, 'sleep')  // 'class method metadata'
```



## 结合装饰器

```ts
@Reflect.metadata('Class', 'class metadata')
class Animal {
    @Reflect.metadata('Class Property', 'class property metadata')
    static type = 'animal'
    @Reflect.metadata('Class Method', 'class method metadata')
    sleep() { }
}
```

实现必选属性校验

```ts
const REQUIRED_KEY = Symbol('REQUIRED_KEY')
function Required(target: any, prop: string) {
    const requireKeys = Reflect.getMetadata(REQUIRED_KEY, target) || []
    Reflect.defineMetadata(REQUIRED_KEY, [...requireKeys, prop], target)
}

class Animal {
    @Required
    name!: string
    @Required
    age!: number
}
const anm = new Animal()

const existsProps = Reflect.ownKeys(anm)
const requireKeys = Reflect.getMetadata(REQUIRED_KEY, anm) || []
for (const key of requireKeys) {
    if (!existsProps.includes(key)) {
        throw new Error(`${key} is required`)
    }
}
```



## 应用 - 简化装饰器

```ts
class Container {
    public instances = new Map()
    public properties = new Map()
    // 负责注册设备
    bind<T>(deviceName: string, creator: () => T) {
        if (!this.instances.has(deviceName)) this.instances.set(deviceName, creator())

        return this.instances.get(deviceName)
    }
    // 从注册的设备中取出设备
    // 如果这个设备有依赖项则对依赖项进行绑定
    resolve<T>(deviceName: string): T {
        const instance = this.instances.get(deviceName)

        for (const property of this.properties) {
            const [key, ServiceKey] = property
            const [classKey, propKey] = key.split(':')

            if (instance.constructor.name !== classKey) continue
            const dep = this.resolve(ServiceKey.name)

            instance[propKey] = dep
        }
        return instance
    }
}

const container = new Container()
// 通过装饰器实施设备注册
// 注册时直接采用类名
function Provide(target: any) {
    container.bind(target.name, () => new target())
}
// 通过装饰器实施依赖项绑定
// 开启 emitDecoratorMetadata 后
// 类型注入时会自动收集注入的实例方法属性的类型 将元数据记录在(design:type TYPE target key)上
function Inject(target: any, key: string) {
    container.properties.set(`${target.constructor.name}:${key}`, Reflect.getMetadata('design:type', target, key))
}

interface Monitor {
    display(): void
}

interface Host {
    start(): void
}

@Provide
class Monitor23inch implements Monitor {
    display() {
        console.log('开启显示器')
    }
}

@Provide
class XiaomiHost implements Host {
    start() {
        console.log('开启主机')
    }
}

@Provide
class Computer {
    @Inject
    public monitor!: Monitor23inch // ('design:type', 'Monitor23inch', Computer, 'monitor')
    @Inject
    public host!: XiaomiHost // ('design:type', 'XiaomiHost', Computer, 'host')

    initialize() {
        this.monitor.display()
        this.host.start()
    }
}

const computer = container.resolve<Computer>('Computer')
computer.initialize()
```



## 应用 - 路由构造

```ts
import 'reflect-metadata'

function methodDecorator(method: string) {
    return function(path: string) {
        return function(target: any, prop: string, descriptor: PropertyDescriptor) {
            Reflect.defineMetadata('method', method, descriptor.value)
            Reflect.defineMetadata('path', path, descriptor.value)
        }
    }
}

function Controller(path: string) {
    return function(target: any) {
        Reflect.defineMetadata('path', path, target)
    }
}

const Get = methodDecorator('get')
const Post = methodDecorator('post')

@Controller('/api')
class APIController {
    @Get('/user')
    getUser() {
        return {
            code: 0,
            data: 'Get User'
        }
    }

    @Post('/register')
    registerUser() {
        return {
            code: 0,
            data: 'Register User'
        }
    }
}

function createRoutes(controller: APIController) {
    const root = Reflect.getMetadata('path', controller.constructor)

    const props = Reflect.ownKeys(Reflect.getPrototypeOf(controller)!).filter(k => k !== 'constructor')
    return props.map(prop => {
        const path = Reflect.getMetadata('path', (controller as any)[prop])
        const method = Reflect.getMetadata('method', (controller as any)[prop])

        return {
            path: `${root}${path}`,
            method,
            handler: (controller as any)[prop]
        }
    })
}

const routes = createRoutes(new APIController())
```

