# Npm 包管理器

## 包

和模块为同一个概念，由第三方个人或团队开发

`npm install package[@v]`

`npm i package[@v]`

根目录下会生成`node_modules`和`package-lock.json`以存放下载的包并记录下它们的信息



### 包管理

在项目根目录中，**必须**提供`package.json`的包管理配置文件，记录与项目有关的配置信息，如项目名称、版本号、描述：

`npm init -y`

由此在共享项目时，方便移除`node_modules`目录，也就是将`node_modules`添加到`.gitignore`忽略文件中

在`package.json`存在时，可以通过命令行快速将依赖包添加到项目中

`npm install`

`npm i`

如果某些包只在开发阶段使用，可以将其添加到`devDependencies`节点中：

`npm i package -D`

`npm i package --save-dev`

通过`npm uninstall package`可以删除指定的包



### 镜像源

+ 查看`npm config get registry`
+ 修改`npm config set registry=https://registry.npm.taobao.org/`

初始镜像源为`https://registry.npmjs.org/`

淘宝镜像源为`https://registry.npm.taobao.org/`

利用`nrm`可以快速切换镜像源

```
npm i -g nrm
nrm ls
nrm use taobao
```

利用`i5ting_toc`可以将`.md`文件转化为页面

```
npm i -g i5ting_toc
# -o 表示转化完毕后打开对应的html文件
i5ting_toc -f ./file.md -o
```



### 包的规范

+ 以单独的目录存在
+ 顶级目录下包含`package.json`包管理配置文件
+ `package.json`中必须包含`name`、`version`、`main`属性，表示包名、版本号、包的入口

```json
{
    "name": "package_name",
    "version": "i.j.k",
    "main": "./package.js"
}
```

`package.json`的其他属性

```json
{
    "description": "包的简要描述",
    "keywords": ["检索包的关键词1", "关键词2", "..."],
    "license": "ISC"
}
```



### 发布包

#### npm账号

```
uername: weige666*2
email: lekaimie@gmail.com
password: rekaime
```



#### 登录

通过`npm login`进行登录操作，输入用户名、密码、邮箱完成登录

在登录前需要先把下包服务器地址切换为npm的官方服务器



#### 发布

`cd 包的顶级目录`

`npm publish`

包名不能雷同



#### 移除

`npm unpublish package --force`

+ `npm unpublish`只能删除**72小时内**发布的包
+ `npm unpublish`删除的包不能于**24小时内**重新发布
+ 不要发布没有意义的包



### 推荐包

+ `nanoid`：类似于`uuid`，可以生成`id`标识

```shell
npm i nanoid
```

```js
import { nanoid } from 'nanoid'
```



# 模块化

## CommonJS

### 导出

`Student.js`

```js
const name = 'rekaime'
const id = '001'

function getName() {
    return name
}

function getHobbies() {
    return ['Learning', 'Playing games', 'Singing']
}

// 单个追加 exports === this
exports.name = name
this.name = name

// 批量导出
module.exports = {
    getName,
    getHobbies
}
```



### 导入

`index.js`

``` js
const student = require('./student.js')
// OR
const { getName, getHobbies } = require('./student.js')
```



### 底层原理

`NodeJS`的代码其实被包装在一个函数中，我们所编写的代码其实是这个函数的函数体

调用`console.log(arguments.callee.toString())`可以查看函数

```js
function (exports, require, module, __filename, __dirname) {
    /* ... */
}
```



### 浏览器端的使用

`NodeJS`默认支持`CommonJS`而浏览器不支持，需要代码编译后才可以在浏览器导入

借助第三方包

```shell
npm i browserify -g

browserify target.js -o build.js
```



## ES6

### 浏览器端的使用

通过`type="module"`可以让浏览器解析`js`资源时，将其中的变量进行隔离

也就是该`js`文件中诞生的变量不再挂载到`windows`上

`<script type="module" src="./index.js"></script>`



### 服务器端的使用

1. 将`.js`后缀名更改为`.mjs`
2. 在`package.json`文件中设置`"type": "module"`



### 导出

#### 概述

以下三种导出方式可以混用，不会造成冲突



#### 分别导出

`student.js`

```js
const name = 'rekaime'
const id = '001'

// 通过 export 标识希望导出的变量名
export function getName() {
    return name
}

export function getHobbies() {
    return ['Learning', 'Playing games', 'Singing']
}
```



#### 统一导出

`student.js`

```js
const name = 'rekaime'
const id = '001'

function getName() {
    return name
}

function getHobbies() {
    return ['Learning', 'Playing games', 'Singing']
}

// 通过 export 指定一系列需要导出的变量名
// 这里的 {} 只是作为标识符 不是对象
export { getName, getHobbies }
```



#### 默认导出

`student.js`

```js
const name = 'rekaime'
const id = '001'

function getName() {
    return name
}

function getHobbies() {
    return ['Learning', 'Playing games', 'Singing']
}

// 通过 export default 指示需要导出的变量名
// 一般导出的内容会被封装在对象中
export default { getName, getHobbies }
```



### 导入

#### 导入全部

`index.js`

```js
// 导入的内容在 student 上可见
// 非默认导出的内容直接挂载在 student 上
// 默认导出的变量 === student.default
import * as school from './student.js'
```



#### 命名导入

`index.js`

```js
import { getName as getStudentName, getHobbies } from './student.js'
```



#### 默认导入

`index.js`

```js
// 导入的是默认导出的内容 所以导入的包必须要有默认导出
import student from './student.js'
```



#### 动态导入

`index.js`

```js
// 一般导入一个即时生效的文件 或者用于异步导入
// 导入一百遍也只生效一次 所以不要导多了
import('./student.js')
```



# Webpack