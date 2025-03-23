# Node.js



## `file`模块

`const fs = require('fs')`

### 读取文件

`fs.readFile(path, [, options], callback)`

`fs.readFileSync(path, [, options])`

+ path: 文件路径

+ option:

  + encoding: 指定文件编码格式等

  + flag: 指定读取模式，默认为`'r'`

+ callback: `fn(err, data)`回调函数，对文件读取的结果进行操作

```js
// 同步
const fileData = fs.readFileSync('./file.txt')
if (fileData) console.log(fileData)

// 异步
fs.readFile('./file.txt', 'utf8', (err, data) => {
    if (err) return console.log(err.message)
    // 对数据 data 进行处理
})
```



### 写入文件

`fs.writeFile(path, data[, options], callback)`

`fs.writeFileSync(path, data[, options])`

`fs.appendFile(path, data[, options], callback)`

`fs.appendFileSync(path, data[, options])`

+ path: 文件路径

+ data: 写入的内容

+ options:

  + encoding: 指定文件编码格式

  + flag: 指定写入模式，`writeFile`默认为`'w'`，`appendFile`默认为`'a'`

+ callback: `fn(err)`文件写入操作完成后的回调函数

```js
// 同步
fs.writeFileSync('./file.txt', 'Hello World!', {flag: 'a'})

// 异步
fs.writeFile('./file.txt', 'Hello World!', 'utf8', err => {
    if (err) console.log(err)
})
```



### 删除文件

`fs.unlink(path, callback)`

`fs.unlinkSync(path)`

+ path: 文件路径

+ callback: `fn(err)`删除文件操作完成后的回调函数

```js
// 同步
fs.unlinkSync('./file.txt');

// 异步
fs.unlink('./file.txt', err => {
    if (err) console.log(err)
})
```



### 操作目录

#### 创建目录

`fs.mkdir(path[, options], callback)`

`fs.mkdirSync(path[, options])`

+ path: 目录路径

+ options:

  + recursive: 是否递归，默认为`false`

  + callback: `fn(err)`回调函数

```js
// 同步
fs.mkdirSync('./dir')

// 异步
fs.mkdir('./dir', err => {
    if (err) console.log(err)
})
```



#### 读取目录

`fs.readdir(path[, options], callback)`

`fs.readdirSync(path[, options])`

+ path: 路径
+ options:
  + encoding: 文件编码格式
  + withFIleTypes: 是否显示文件类型，默认为`false`
+ callback: `fn(err, files)`回调函数

```js
// 同步
const fileList = fs.readdirSync('./dir')

// 异步
fs.readdir('./dir', (err, files) => {
    if (err) return console.log(err)
    // 对文件进行操作
})
```



#### 删除目录

`fs.rmdir(path, callback)`

`fs.rmdirSync(path)`

+ path: 路径
+ callback: `fn(err)`回调函数

```js
// 同步
fs.rmdirSync('./dir')

// 异步
fs.rmdir('./dir', err => {
    if (err) console.log(err)
})
```



### 其他操作

#### 删除文件或文件夹

`fs.rm(path[, options], callback)`

`fs.rmSync(path[, options])`

+ path: 路径
+ options：
  + force: 是否忽略异常，默认为`false`
  + recursive: 是否递归删除，默认为`false`，递归删除时，操作将在失败时进行重试
  + maxRetries: 重试次数，默认为`0`
  + retryDelay: 重试之间的等待间隔`ms`，默认为`false`
+ callback: `fn(err)`回调函数

```js
// 同步
fs.rmSync('./dir/di/d', { recursive: true })

// 异步
fs.rm('./dir/di/d', { recursive: true }, err => {
    if (err) console.log(err)
})
```



#### 重命名文件或文件夹

`fs.rename(oldPath, newPath, callback)`

`fs.renameSync(oldPath, newPath)`

+ oldPath: 原路径
+ newPath: 新路径
+ callback: `fn(err)`回调函数

```js
// 同步
fs.renameSync('./old.txt', './new.txt')

// 异步
fs.rename('./old.txt', './new.txt', err => {
    if (err) console.log(err)
})
```



#### 查看文件状态

`fs.stat(path[, options], callback)`

`fs.statSync(path[, options])`

+ path: 路径
+ options:
  + bigint: `status`中的数值是否为`bigint`，默认为`false`
+ callback: `fn(err, status)`回调函数

其中 status 为 `fs.Stats`对象，属性为对应文件的信息：

+ isDirectory(): 查看对象是否为目录
+ isFile(): 查看对象是否为文件
+ size: 大小

```js
// 同步
const status = fs.statSync('./file.txt')

// 异步
fs.stat('./file.txt', (err, status) => {
    if (err) return console.log(err)
    // 相关操作
})
```



#### 截取文件内容

`fs.truncate(path[, len], callback)`

`fs.truncateSync(path[, len])`

+ path: 路径
+ len: 将文件截取到指定大小`byte`，默认为`0`
+ callback: `fn(err)`回调函数

```js
// 同步
fs.truncateSync('./file.txt', 5)

// 异步
fs.truncate('./fiel.txt', 5, err => {
    if (err) console.log(err)
})
```



#### 监听文件数据变化

`fs.watchFile(filename[, options][, listener])`

+ filename: 路径
+ options:
  + bigint: 默认为`false`
  + persistent: 正在监听文件时，进程是否继续游戏，默认为`true`
  + interval: 监听的时间间隔，默认为`5007`
+ listener: `fn(current, previous)`回调函数，参数为修改前后的状态



#### 监听文件或文件夹变化

`fs.watch(filename[, options][, listener])`

+ filename: 路径
+ options:
  + encoding: 指定传递给监听器的文件名的字符编码
  + persistent: 正在监听文件时，进程是否继续游戏，默认为`true`
  + recursive: 是否监听所有子目录，还是仅监听当前目录，默认为`false`，仅在 MacOS 和 Windows 系统上受支持
+ listener: `fn(eventTYpe, filename)`回调函数，event: rename / change，filename：触发事件的文件(夹)名称



## `path`模块

`const path = require('path')`

### 拼接路径

`path.join([...paths])`

```js
path.join(__dirname, 'dir', 'file.txt')
```



### 获取目录名

`path.dirname(path)`



### 获取文件名

`path.basename(path)`



### 获取扩展名

`path.extname(path)`



## `http`模块

`const http = require('http')

### 创建服务器`

```js
const server = http.createServer()
server.on('request', (req, res) => {
    console.log('接收到客户端请求')
})
server.listen(port, () => {
    console.log('Server running at http://127.0.0.1:8080')
})
```



### 响应

响应内容并结束请求: `res.end(str)`

设置响应头: `res.setHeader(attr, val)`

```js
server.on('request', (req, res) => {
    // 相应处理
    const url = req.url
    const fpath = url === '/' ? path.join(__dirname, './index') : path.join(__dirname, url)
   	
    fs.readFile(fpath, 'utf8', (err, data) => {
        if (err) return res.end('<h2>404 Not Found.</h2>')
        // 返回结果到网页
        res.setHeader('Content-Type', 'text/html; charset=utf-8')
        res.end(data)
    })
    
})
```



## 模块化

+ 内置模块：`fs`、`path`、`http`...
+ 自定义模块：自己写的`js`文件
+ 第三方模块：需要下载

```js
// 内置
const fs = require('fs')
// 自定义
const utils = require('./utils')
// 第三方
const express = require('express')
```



### 定义模块

```js
module.exports = {
    // ...
}
```



### 模块加载机制

#### 缓存

模块加载时优先从缓存读取，缓存中不存在的模块才会从其他目录载入



#### 优先级

内置模块 > 第三方模块 



#### 自定义模块加载机制

+ 必须包含`./`或者`../`，否则`node`会将其视为内置模块或第三方模块
+ 如果缺失文件扩展名，按顺序尝试加载`require()`中提供的文件：
  1. 按照确切的文件名加载
  2. 补全`.js`进行加载
  3. 补全`.json`进行加载
  4. 补全`.node`进行加载
  5. 加载失败则报错



#### 第三方模块加载机制

从当前模块所在目录开始，尝试在`/node_modules/`目录下寻找对应的包

如果没找到，就回退到当前模块所在目录的上一级继续进行尝试

重复以上步骤直到系统的根目录



#### 加载目录

如果目录作为模块标识符，传递给`require()`进行加载，则会进行如下操作：

* 在目录下查找`package.json`，并尝试在其中寻找`main`作为模块入口
* 若上述操作无法完成，则尝试加载该目录下的`index.js`文件作为模块入口
* 若上述操作无法完成，则输出错误信息



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

```shell
npm i -g nrm
nrm ls
nrm use taobao
```

利用`i5ting_toc`可以将`.md`文件转化为页面

```shell
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

```shell
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



# Express

`npm i express`



## 简易服务器搭建

### 搭建基本的 Web 服务器

```js
const express = require('express')
const app = express()

app.listen(80, () => {
    console.log('Server running at http://127.0.0.1:80')
})
```



### 监听请求

```js
app.get(url, function (req, res) {
    // 数据处理
    
    // 查询字符串 ?key=value
    console.log(req.query)
   	// 动态参数 :id
    console.log(req.params)
    
    res.send({
        name: "rekaime",
        age: 18,
        gender: 0
    })
})

app.post(url, function (req, res) {
	// 数据处理
    
    res.send('请求成功!')
})
```



### 静态资源

```js
// dir/css/base.css
// dir/js/base.js
// dir/image/img.jpg

// 可反复调用以托管多个静态资源
app.use(express.static('./dir'))
```

```html
<style src="./css/base.css"></style>
<img src="./image/img.jpg">
<script src="./js/base.js"></script>
```

若需要挂载路径前缀，可以进行如下操作

```js
app.use('/dir', express.static('./dir'))
```



### 路由匹配机制

根据`method`和`url`进行逐个匹配，先定义的路由将优先被匹配到



## 路由模块化

### 创建路由模块

```js
const express = require('express')
const router = express.Router()

router.get('/', (req, res) => {})
router.post('/', (req, res) => {})

module.exports = router
```



### 注册路由模块

```js
const routerModule = require('./router/user.js')

app.use(routerModule)

// 同样可以添加前缀
app.use('api', routerModule)
```



## 中间件

### 调用

当请求到达`Express`服务器后，可以连续调用多个中间件进行请求的预处理



### 中间件的格式

本质上是一个`function(req, res, next)`，被称作**middleware function

区分中间件和路由，可以通过观察函数列表中是否包含`next`参数

`next`函数负责将流转关系转交给下一个**中间件**或**路由**

多个中间件之间会共享同一份`req`和`res`



### 全局生效的中间件

客户端发起任何请求，到达服务器时都会触发的中间件

```js
// 第一个触发的全局中间件
app.use((req, res, next) => {})
// 第二个触发的全局中间件
app.use((req, res, next) => {})
// ...
```



### 局部生效的中间件

```js
// 不适用 app.use() 定义
// 包含有局部中间件
app.get('/', mw1, mw2, ..., (req, res) => {})

// 或者写作
app.get('/', [mw1, mw2, ...], (req, res) => {})
```



### 注意事项

+ 在路由创建前注册中间件
+ 客户端发送的请求可以连续调用多个中间件进行处理
+ 执行完中间件业务代码后，一定要调用`next()`
+ 为防止**代码逻辑混乱**，调用`next()`后不要再写额外的代码



### 中间件的分类

+ 应用级别中间件
+ 路由级别中间件
+ 错误级别中间件
+ Express内置中间件
+ 第三方中间件

#### 应用级别中间件

通过`app.use()`或`app.get()`、`app.post()`绑定到`app`上的中间件



#### 路由级别中间件

绑定到`express.Router()`上的中间件



#### 错误级别中间件

必须注册于所有路由之后

用于捕获项目中发生的异常错误，防止项目异常崩溃的问题

其`function`包含四个形参`(err, req, res, next)`

```js
app.get('/', (req, res) => {
    throw new Error('服务器发生错误')
    res.send('OK')
})

app.use((err, req, res, next) => {
    console.log('发生错误')
    console.log(err.message)
    res.send('Error')
})
```



#### Express内置中间件

+ `express.static`快速托管静态资源
+ `express.json`解析JSON格式的请求体数据
+ `express.urlencoded`解析URL-encoded格式的请求体数据

```js
// 配置 application/json 格式数据的内置中间件
// 否则 req.body === undefined
app.use(express.json())
// 配置 application/x-www-form-urlencoded 格式数据的内置中间件
app.use(express.urlencoded({ extended: false }))
```



#### 第三方中间件

以`body-parser`为例

`npm install body-parser`

```js
// Express 内置的 express.urlencoded 中间件
// 是基于 body-parser 封装的
const bodyParser = require('body-parser')

app.use(bodyParser.urlencoded({ extended: false }))
```



### 自定义中间件实例

```js
// 中间件函数封装
const qs = require('querystring') // 内置模块

const bodyParser = (req, res, next) => {
    let str = ''
    
    req.on('data', chunk => {
        str += chunk
    })
    
    req.on('end', () => {
        // console.log(str)
        const body = qs.parse(str)
        req.body = body
        next()
    })
}

module.exports = bodyParser
```



```js
const express = require('express')
const app = express()
const customBodyParser = require('./customBodyParser')

app.use(customBodyParser)

app.post('/user', (req, res) => {
    res.send(req.body)
})
```



## 接口

### 创建 API 路由模块

利用`express.Router()`创建路由对象

对路由对象绑定相应的路由

然后将路由对象导出：`module.exports = apiRouter`

在`app.js`中导入路由对象并注册：`app.use('/api', apiRouter)`



### GET 接口

```js
router.get('/get', (req, res) => {
    const query = req.query
    res.send({
        status: 0,
        msg: 'GET 请求成功',
        data: query
    })
})
```



### POST 接口

```js
// 在 app.js 中配置解析表单数据的中间件
app.use(express.urlencoded({ extended: false }))
```



```js
router.post('/post', (req, res) => {
    const body = req.body
    res.send({
        status: 0,
        msg: 'POST 请求成功',
        data: body
    })
})
```



## 跨域

### 原因及解决方案

**协议**、**域名**、**端口号**任何一项不同就会出现跨域问题

主要解决方案有两种：

+ CORS (Cross-Origin Resource Sharing)：主流方案
+ JSONP：只支持GET请求



### CORS 中间件简单使用

浏览器的**同源安全策略**会阻止网页的跨域获取资源

如果接口服务器配置了 CORS 相关的 HTTP 响应头，就可以解决浏览器端的跨域访问限制

```shell
npm i cors
```

```js
 const cors = require('cors')
 // 在路由前调用，配置中间件
 app.use(cors())
```

注意事项：

+ CORS 只需在服务器端进行配置，客户端无需任何额外配置
+ 兼容性：支持 XMLHttpRequest Level2 的浏览器才可以正常访问开启 CORS 的服务端接口（IE10+、Chrome4+、FireFox3.5+）



### CORS 响应头

格式：`Access-Control-Allow-XXX`



#### Access-Control-Allow-Origin

`Access-Control-Allow-Origin: <origin> | *`

`<origin>`为允许访问该资源的外域 URL



#### Access-Control-Allow-Headers

默认情况下 CORS 仅支持客户端向服务器发送9类请求头：

`Accept`、`Accept-Language`、`Content-Language`、`DPR`、`Downlink`、`Save-Data`、`Viewport-Width`、`Width`、`Content-Type`（值仅限于`text/plain`、`multipart/form-data`、`application/x-www-form-urlencoded`）

若客户端向服务器发送额外请求头信息，则需要在服务器端设置额外的请求头声明：

`res.setHeader('Access-Control-Allow': 'Content-Type, X-Custom-Header')`



#### Access-Control-Allow-Methods

默认情况下，CORS仅支持客户端发起 GET、POST、HEAD 请求

如果客户端希望发起 PUT、DELETE 请求，则需要在服务器端先行配置

`res.setHeader('Access-Control-Allow-Methods', 'GET, POST, HEAD, DELETE')`

`res.setHeader('Access-Control-Allow-Methods', '*')`



### CORS 请求的分类

#### 简单请求

满足以下两个条件：

+ 请求方式为 GET、POST、HEAD三者之一
+ HTTP头部为无自定义头部字段或9种默认允许的请求头



#### 预检请求

满足以下任一条件：

+ 请求方式为 GET、POST、HEAD 之外的类型
+ 请求头包含自定义头部字段
+ 向服务器发送`application/json`格式的数据

在浏览器与服务器正是通信前，浏览器会先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求

该次 OPTION 请求成为**预检请求**，服务器成功响应预检请求后，才会发送真正的请求



### JSONP 接口

#### 概念

浏览器通过`<script>`标签的`src`属性请求服务器上的数据

同时服务器返回一个函数的调用

特点：

+ 不属于真正的 Ajax 请求，因为没用到 XHR 对象
+ 仅支持 GET 请求

如果项目中已经配置了 CORS，为了防止冲突，需要在 CORS 中间件注册前声明 JSONP 接口，否则 JSONP 接口会被处理成开启了 CORS 的接口：

`app.get('/api/jsonp', (req, res) => {})`



#### 实现步骤

+ 获取客户端发送的回调函数名称
+ 获取要通过 JSONP 形式发送给客户端的数据
+ 根据前两步得到的数据，拼接出一个函数调用的字符串
+ 把该字符串响应给客户端的`<script>`标签解析执行

```js
app.get('/api/jsonp', (req, res) => {
	const funcName = req.query.callback
    const data = {name: 'Leo', age: 18}
    const scriptStr = `${funcName}(${JSON.stringify(data)})`
    res.send(scriptStr)
})
```

```js
// JQuery
$.ajax({
    method: 'GET',
    url: 'http://127.0.0.1/api/jsonp',
    dataType: 'jsonp',
    success: function (res) {
        console.log(res)
    }
})
```

