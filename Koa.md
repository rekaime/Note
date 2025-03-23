# 概述

[点击进入官网](https://koajs.com)

[点击进入中文版官网](https://koa.bootcss.com)

[GitHub 仓库](https://github.com/koajs/koa)

特点：

+ 结构和 Express 相似，语法进行了升级
+ 内部使用 ES6 编写
+ 利用`async`，丢弃回调函数
  + Koa 1 基于 ES6 的 Generator + co 模块
  + Koa 2 升级为`async/await`函数，异常处理更简单
+ 提供了 CTX 上下文对象（Express 是扩展了`req`和`res`）
+ 不捆绑任何中间件，而是采用更为优雅的写法
+ 很多开发工具和框架基于 Koa
  + Egg.js
  + Vite

[koa-vs-express.md](https://github.com/koajs/koa/blob/master/docs/koa-vs-express.md)

[awesome-koa Koa about repository](https://github.com/ellerbrock/awesome-koa)



# 搭建简单的服务器

## 下载

```shell
npm init -y
npm i koa
```



## 搭建

```js 
const Koa = require('koa')
const app = new Koa()

// Koa 不存在路由 只有中间件
// ctx 上下文对象
app.use(ctx => {
    ctx.body = 'Hello Koa！'
})

app.listen(7777, () => {
    console.log('Server running on http://localhost:7777')
})
```



# App

## 配置

在创建`app`实例的同时可以进行配置

```js
const Koa = require('koa')
const app = new Koa({ proxy: true })
// OR
const Koa = require('koa')
const app = new Koa()
app.proxy = true
```



### app.env

`app.env`默认为`NODE_ENV`环境变量的值，未定义则默认为`"development"`（标识应用运行环境为`development`（开发环境）、`production`（生产环境）或`test`（测试环境））



### app.keys

`app.keys`为一个数组，包含用于 cookie 签名的密钥

使用`ctx.cookie`方法设置签名 cookie 时需要提供密钥集

```js
app.keys = ['key1', 'key2', 'key3']
ctx.cookies.set('name', 'value', { signed: true })
```

传入的数组最终会被转化为`KeyGrip`，因此可接受的值可以为`KeyGrip`

```js
app.keys = ['OEK5zjaAMPc3L6iK7PyUjCOziUH3rsrMKB9u8H07La1SkfwtuBoDnHaaPCkG5Brg', 'MNKeIebviQnCPo38ufHcSfw3FFv8EtnAe1xE02xkN1wkCV1B2z126U44yk2BQVK7']
// OR
app.keys = new KeyGrip(['OEK5zjaAMPc3L6iK7PyUjCOziUH3rsrMKB9u8H07La1SkfwtuBoDnHaaPCkG5Brg', 'MNKeIebviQnCPo38ufHcSfw3FFv8EtnAe1xE02xkN1wkCV1B2z126U44yk2BQVK7'], 'sha256')
```





### app.proxy

默认值为`false`，当值为`true`时信任代理头字段

如果应用部署在反向代理（如 Nginx 或 HAProxy）之后，可以通过设置`app.proxy = true`信任代理头中的信息，如`X-Forwarded-For`（真实客户端 IP）

信任代理后，可以使用代理头字段处理客户端的真实 IP 和协议



### app.subdomainOffset

忽略子域名`app.subdomains`的偏移量，默认为 2

处理子域名时，将会忽略`app.subdomainOffset`个子域名

例如对于域名`www.rekaime.com`：

+ `app.subdomainOffset = 2`则`.subdomains`为`['www']`
+ `app.subdomainOffset = 1`则`.subdomains`为`['www', 'rekaime']`



### app.proxyIpHeader

代理 IP 头字段名称，默认为 `X-Forwarded-For`

当`app.proxy === true`时，通过此字段可以获取到客户端真实 IP



### app.maxIpsCount

从代理 IP 头字段中读取的最大 IP数，默认为 0（无限制）

设置较小的值以限制读取，避免恶意伪造的长 IP 列表耗尽资源



## app.listen( )

`app.listen()`实际上是一种语法糖

```js
const Koa = require('koa')
const app = new Koa()
app.listen(7777)

// 等同于
const Koa = require('koa')
const app = new Koa()
const http = require('http')

http.createServer(app.callback()).listen(7777)
```

因此可以将同一个`app`启动于`http`、`https`或多个地址上

关于`app.callback()`，会返回适用于`http.createServer()`的回调函数（`(req, res) => { ... }`）

这个回调函数也可以被挂载到 Connect / Express `app`上



## app.use( )

`app.use(function)`可以将中间件函数挂载到`app`上

返回值为`app`，因此可以采用链式写法



## app.context

`app.context`是创建`ctx`的原型

可以在原型上添加属性及方法帮助开发

```js
console.log(Reflect.getPrototypeOf(ctx) === app.context) // true
```



## 错误处理

默认情况下错误将输出到`stderr`（除非`app.silent === true`）

在状态码`err.status`为`404`或`err.expose === true`时，默认的错误处理逻辑不会输出错误内容

因此需要自定义一个错误处理逻辑作为集成日志

`app.on('error')`可以处理全局异常，如果报错没有被错误处理逻辑捕获，则会在全局进行处理

但是不要在这里面修改`ctx`，因为无法保证此时响应是否发送

```js
app.on('error', err => {
    log.error(err)
})
```

在请求或响应期间发生的错误，还可以接收到`ctx`对象

```js
app.on('error', (err, ctx) => {
    log.error(err)
})
```

错误发生时，Koa 会以状态码 500 响应客户端

利用洋葱模型，我们可以在最外层编写错误处理逻辑

```js
app.use(async (ctx, next) => {
    try {
        await next()
    } catch (err) {
        ctx.status = 500
        ctx.body = '服务器错误'
    }
})
```



## 级联

通过`async`可以实现中间件级联

```js
// logger
app.use(async (ctx, next) => {
    await next()
    const rt = ctx.response.get('X-Response-Time')
    console.log(`${ctx.method} ${ctx.url} - ${rt}`)
})

// x-response-time
app.use(async (ctx, next) => {
    const start = Date.now()
    await next()
    const ms = Date.now() - start
    ctx.set('X-Response-Time', `${ms}ms`)
})

// data
app.use(async ctx => {
    ctx.body = {
        data: 'Hello Koa!'
    }
})
```

```bash
$ GET / - 1ms
```



# Context

## 概述

Koa 的`ctx`封装了 node 的`request`和`response`，同时提供一定的开发方法

```js
app.use(async ctx => {
    ctx.req // node's object
    ctx.res // node's object
    ctx.request  // Koa Request
    ctx.response // Koa Response
})
```

需要注意，Koa 不支持绕过 Koa 本身做出响应，因此需要避免使用原生 node 方法：

+ `res.statusCode`
+ `res.writeHead()`
+ `res.write()`
+ `res.end()`



## API

除了`ctx.req`、`ctx.res`、`ctx.request`、`ctx.response`以外的其他 Koa context API



### ctx.state

推荐采用这种命名空间从中间件或者前端视图获取信息

一般用于简化上下文的信息传递，表现在中间件之间的**数据共享**

```js
// 设置当前用户的信息
app.use(async (ctx, next) => {
    ctx.state.user = { id: ‘001, name: 'rekaime' }
    await next()
})

// 对用户数据进行处理
app.use(async (ctx, next) => {
    ctx.body = `Hello, ${ctx.state.user.name}`
})
```

在使用`koa-views`等模板引擎时，`ctx.state`可以配合模板渲染，将数据传递给模板

```js
const Koa = require('koa')
const views = require('koa-views')
const app = new Koa()

// 配置模板引擎
app.use(views(__dirname + '/views', { extension: 'ejs' }))

// 设置 ctx.state
app.use(async (ctx, next) => {
    ctx.state = {
        title: 'Koa Demo',
        user: { name: 'Alice' }
    }
    await next()
})

// 渲染模板
app.use(async ctx => {
    await ctx.render('index') // index.ejs 会使用 ctx.state 数据
})

app.listen(3000)
```

`index.ejs`

```ejs
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <h1>Hello, <%= user.name %>!</h1>
</body>
</html>
```



### ctx.app

`ctx.app === app`



### ctx.app.emit( )

`ctx.app.emit(event, ...args)`是事件触发器，遵循 Node.js 事件模型，机制类似于 Node.js 的 [EventEmitter](https://nodejs.org/dist/v11.15.0/docs/api/events.html)

该方法第一个参数为事件名称

后续为触发事件回调的参数

`ctx.app.emit`常用于触发自定义事件和进行错误处理，还可以跨中间件处理数据以及执行异步任务

通过这一方法可以实现逻辑解耦、灵活处理逻辑以及统一管理业务

自定义事件：

```js
// 注册事件监听器
app.on('SayHi', name => {
    console.log(`Hello, ${name}`)
})

app.use(async (ctx, next) => {
    ctx.app.emit('SayHi', 'rekaime') // 触发事件
    await next()
})
```

错误处理：

```js

// 监听错误事件
app.on('error', (err, ctx) => {
    console.error('Error occurred:', err.message)
    console.error('Context info:', ctx.url)
});

// 中间件：模拟错误
app.use(async (ctx, next) => {
    throw new Error('Something went wrong!')
})

// 错误处理中间件
app.on('error', (err, ctx) => {
    console.error('An error was caught and logged.')
    ctx.status = 500
    ctx.body = 'Internal Server Error'
})
```



### ctx.cookies

`ctx.cookies`属性是基于 [cookies](https://github.com/pillarjs/cookies) 模块实现的

通过`cookies.get(name[, options])`可以获取指定的 cookie

常用配置为：

+ `signed`：值为`true`则会对 cookie 进行签名验证，如果 cookie 的签名不正确则返回`undefined`
+ `decode`：自定义解码函数，用于解码 cookie，默认为`decodeURIComponent`

```js
app.use(async ctx => {
    // 获取客户端传来的 cookie
    const username = ctx.cookies.get('username')
    ctx.body = `Hello, ${ username || 'Guest' }`
})

app.use(async ctx => {
    // 验证签名是否有效
    const username = ctx.cookies.get('username', { signed: true })
    ctx.body = `Hello, ${ username || 'Guest' }`
})
```

通过`cookies.set(name, value[, options])`可以向客户端设置 cookie，然后通过响应头发送给客户端

常用配置为：

+ `maxAge`：设置 cookie 的最大存活时间（单位：毫秒），超时则 cookie 过期
+ `expires`：设置 cookie 的过期时间，通常为`Date`对象，表示在何时过期
+ `httpOnly`：值为`true`则 cookie 只能通过 HTTP 请求访问，不能通过 JavaScript 访问（提高安全性）
+ `secure`：值为 `true`则 cookie 只有在 HTTPS 连接下才能被发送
+ `domain`：设置 cookie 的作用域（即允许发送的域名）
+ `path`：设置 cookie 的路径，限制 cookie 只能在指定路径下发送
+ `signed`：值为`true`则 cookie 会被签名，Koa 会对其进行签名验证



### ctx.throw( )

`ctx.throw([status[, msg[, properties]]])`可以抛出 HTTP 异常

抛出 HTTP 异常时，Koa 会终止中间件的处理流程，并根据错误处理中间件响应内容

可以指定状态码，默认状态码为`500`

`msg`会作为错误响应的`body`

`properties`会合并到`err`对象身上



### ctx.assert( )

`ctx.assert(flag[, status[, msg[, properties]]])`可以进行断言

当`flag === false`时抛出错误（同`ctx.throw()`）



### ctx.respond

通常情况不建议使用，因为可能破坏中间件链，与预期实现的功能不相符

设置`ctx.respond = false`，则 Koa 不会自动生成响应，相当于回归了原生的 Http 处理方法

 `ctx.respond`为`true`时，Koa 会自动处理响应，包括设置 HTTP 状态码、设置响应头、处理响应体等

`ctx.respond`为`false`时，Koa 会跳过自动响应的步骤，你需要手动设置响应的各个方面

```js
app.use(async (ctx) => {
    ctx.respond = false

    // 手动控制响应
    ctx.status = 200
    ctx.set('Content-Type', 'text/plain')
    ctx.body = 'Hello, Koa!'
    // 此时 Koa 不会自动发送响应，开发者必须手动控制
})
```

经典应用场景为流式响应

如果需要返回一个流（比如文件下载、视频流等），需要禁用 Koa 的默认响应行为，手动控制响应的发送

例如，设置响应头以支持分块传输编码或处理文件流的写入

```js
app.use(async (ctx) => {
    ctx.respond = false

    const stream = fs.createReadStream('large-file.txt')
    ctx.status = 200
    ctx.set('Content-Type', 'text/plain')
    ctx.set('Content-Length', fs.statSync('large-file.txt').size)

    stream.pipe(ctx.res) // 手动将流数据发送到客户端
});
```



## alias

在`ctx`上挂载的属性方法，和`ctx.request`以及`ctx.response`上挂载的同名属性方法等效

+ Request：
  + `ctx.header`
  + `ctx.headers`
  + `ctx.method`
  + `ctx.method=`
  + `ctx.url`
  + `ctx.url=`
  + `ctx.originalUrl`
  + `ctx.origin`
  + `ctx.href`
  + `ctx.path`
  + `ctx.path=`
  + `ctx.query`
  + `ctx.query=`
  + `ctx.querystring`
  + `ctx.querystring=`
  + `ctx.host`
  + `ctx.hostname`
  + `ctx.fresh`
  + `ctx.stale`
  + `ctx.socket`
  + `ctx.protocol`
  + `ctx.secure`
  + `ctx.ip`
  + `ctx.ips`
  + `ctx.subdomains`
  + `ctx.is()`
  + `ctx.accepts()`
  + `ctx.acceptsEncodings()`
  + `ctx.acceptsCharsets()`
  + `ctx.acceptsLanguages()`
  + `ctx.get()`
+ Response
  + `ctx.body`
  + `ctx.body=`
  + `ctx.status`
  + `ctx.status=`
  + `ctx.message`
  + `ctx.message=`
  + `ctx.length=`
  + `ctx.length`
  + `ctx.type=`
  + `ctx.type`
  + `ctx.headerSent`
  + `ctx.redirect()`
  + `ctx.attachment()`
  + `ctx.set()`
  + `ctx.append()`
  + `ctx.remove()`
  + `ctx.lastModified=`
  + `ctx.etag=`



# Request API

## header

请求头

还有个`headers`，是`header`的别名



## method

顾名思义



## length

返回`Content-Length`，如果没有这个字段则返回`undefined`



## url

顾名思义



## URL

获取 [WHATWG](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_the_whatwg_url_api) 转化的`URL`对象

```js
// from http://localhost:7777/hello/world?q=1
URL {
  href: 'http://localhost:7777/hello/world?q=1',
  origin: 'http://localhost:7777',
  protocol: 'http:',
  username: '',
  password: '',
  host: 'localhost:7777',
  hostname: 'localhost',
  port: '7777',
  pathname: '/hello/world',
  search: '?q=1',
  searchParams: URLSearchParams { 'q' => '1' },
  hash: ''
}
```





## originalUrl

`http://localhost:7777/hello/world?q=1` => `/hello/world?q=1`



## origin

获取原始 URL，包括协议以及主机名

`http://localhost:7777/hello/world?q=1` => `http://localhost:7777`



## href

获取完整的请求 URL，包括协议、主机名、url

`http://localhost:7777/hello/world?q=1` => `http://localhost:7777/hello/world?q=1`



## protocol

获取请求协议

`http://localhost:7777/hello/world?q=1` => `http`

如果`app.proxy === true`则采用`X-Forwarded-Host`



## path

获取请求路径

`http://localhost:7777/hello/world?q=1` => `/hello/world`



## querystring

获取查询字符串，不含`?`

`http://localhost:7777/hello/world?q=1` => `q=1`



## query

将查询字符串转为对象

查询字符串为空串时返回空对象`{}`



## search

获取查询字符串，含`?`

`http://localhost:7777/hello/world?q=1` => `?q=1`



## host

获取主机名，形式为`hostname:port`

`http://localhost:7777/hello/world?q=1` => `localhost:7777`

如果`app.proxy === true`则采用`X-Forwarded-Host`



## hostname

`http://localhost:7777/hello/world?q=1` => `localhost`

如果`app.proxy === true`则采用`X-Forwarded-Host`

如果主机为 IPv6，则 hostname 的解析将交由 [WHATWG URL API](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_the_whatwg_url_api)，会对性能产生影响



## subdomains

通过数组返回子域名

主域名通常域名被认为是主机名的最后两部分，可以通过`app.subdomainOffset`修改

`"tobi.ferrets.example.com"` => `["ferrets", "tobi"]`



## ip

获取请求 IP

如果`app.proxy === true`则采用`X-Forwarded-Host`



## ips

如果`app.proxy === true`时，如果`X-Forwarded-Host`存在，则会返回一个数组，从上游到下游将代理 ip 排序



## type

获取`Content-Type`，不含`charset`等参数（其实是获取 *mime-type*，[源码](https://github.com/koajs/koa/blob/eda27608f7d39ede86d7b402aae64b1867ce31c6/lib/request.js#L639)）

`content-type: "application/json; charset=utf-8"` => `"application/json"`



## charset

获取`charset`，不存在该字段则返回`undefined`

`content-type: "application/json; charset=utf-8"` => `"utf-8"`



## fresh

判断请求是否包含有效的缓存信息，或者说是否可以根据客户端的缓存状态直接返回 304 Not Modified 响应

基于 HTTP 缓存机制的条件请求头`If-Modified-Since`和`If-None-Match`判断客户端的缓存是否仍然有效

这样可以避免资源重新发送

```js
app.use(async (ctx) => {
    // 设置 Last-Modified 和 ETag 头部
    const lastModified = new Date('2024-01-01')
    const etag = '12345' // 资源版本
    ctx.set('Last-Modified', lastModified.toUTCString())
    ctx.set('ETag', etag)
    // 设置 Cache-Control 来控制缓存
    ctx.set('Cache-Control', 'public, max-age=3600')  // 资源缓存 1小时

    // 检查请求是否“新鲜”
    if (ctx.request.fresh) {
        // 请求是“新鲜”的，返回 304 Not Modified 响应
        ctx.status = 304
    } else {
        // 请求是“陈旧”的，重新发送完整的资源
        ctx.status = 200
        ctx.body = 'Hello, Koa!'
    }
})
```



## stale

`fresh`的非值



## secure

`ctx.protocol == "https"`的简写



## socket

返回请求套接字



## idempotent

判断当前请求是否幂等

请求的幂等指的是无论请求执行多少次，响应结果都相同

一般而言只有 POST 方法是非幂等的



## get( )

`get(field)`可以获取请求头指定字段名的对应值，`field`不区分大小写

常见字段名：

+ **`User-Agent`**：客户端的浏览器或应用程序的信息
+ **`Content-Type`**：请求体的类型（例如 `application/json` 或 `text/html`）
+ **`Authorization`**：认证信息，用于身份验证（通常是 Bearer Token 或 Basic Authentication）
+ **`Accept`**：客户端接受的内容类型
+ **`X-Forwarded-For`**：代理服务器通过该字段传递客户端的真实 IP 地址
+ **`Referer`**：表示从哪个页面链接到当前请求的 URL
+ **`Cookie`**：客户端发送的所有 cookies



## is( )

检查请求是否包含 Content-Type 以及是否包含所给定的 mime-type

如果不含请求体，返回`null`

如果不含 Content-Type 或对应的 mime-type，返回`false`

```js
// With Content-Type: text/html; charset=utf-8
ctx.is('html') // => 'html'
ctx.is('text/html') // => 'text/html'
ctx.is('text/*', 'text/html') // => 'text/html'

// When Content-Type is application/json
ctx.is('json', 'urlencoded') // => 'json'
ctx.is('application/json') // => 'application/json'
ctx.is('html', 'application/*') // => 'application/json'

ctx.is('html') // => false
```



## Content Negotiation

以下方法在无参数时返回所有支持类型

在接受多个参数时返回最佳匹配项，如果无匹配项则返回`false`（此时需要响应 406 "Not Acceptable"）

如果请求头缺少 accept headers，则提供的第一个参数会被返回，因此参数顺序非常重要



### accepts( )

`request.accepts(types)`将检查给定的`type(s)`是否支持

```js
// Accept: text/html
ctx.accepts('html') // "html"

// Accept: text/*, application/json
ctx.accepts('html')             // "html"
ctx.accepts('text/html')        // "text/html"
ctx.accepts('json', 'text')     // "json"
ctx.accepts('application/json') // "application/json"

// Accept: text/*, application/json
ctx.accepts('image/png') // false
ctx.accepts('png')       // false

// Accept: text/*;q=.5, application/json
ctx.accepts(['html', 'json']) // "json"
ctx.accepts('html', 'json')   // "json"

// No Accept header
ctx.accepts('html', 'json') // "html"
ctx.accepts('json', 'html') // "json"
```



### acceptsEncodings( )

`request.acceptsEncodings(encodings)`将检查编码类型是否支持

`identity`作为默认允许的编码类型

```js
// Accept-Encoding: gzip
ctx.acceptsEncodings('gzip', 'deflate', 'identity')
// "gzip"

// Accept-Encoding: gzip, deflate
ctx.acceptsEncodings()
// ["gzip", "deflate", "identity"]
```



### acceptsCharsets( )

`request.acceptsCharsets(charsets)`将检查字符集是否支持

```js
// Accept-Charset: utf-8, iso-8859-1;q=0.2, utf-7;q=0.5
ctx.acceptsCharsets('utf-8', 'utf-7')
// "utf-8"

ctx.acceptsCharsets()
// ["utf-8", "utf-7", "iso-8859-1"]
```



### acceptsLanguages( )

`request.acceptsLanguages(langs)`将检查语言类型是否支持

```js
// Accept-Language: en;q=0.8, es, pt
ctx.acceptsLanguages('es', 'en')
// "es"

ctx.acceptsLanguages()
// ["es", "pt", "en"]
```



# Response API

## header

请求头

还有个`headers`，是`header`的别名



## socket

获取响应套接字，指向`net.Socket`，也就是`request.socket === response.socket`



## status

响应状态码，默认为 404（区别于 Node.js 默认为 200）

以下是状态码参考：

- 100 "continue"
- 101 "switching protocols"
- 102 "processing"
- 200 "ok"
- 201 "created"
- 202 "accepted"
- 203 "non-authoritative information"
- 204 "no content"
- 205 "reset content"
- 206 "partial content"
- 207 "multi-status"
- 208 "already reported"
- 226 "im used"
- 300 "multiple choices"
- 301 "moved permanently"
- 302 "found"
- 303 "see other"
- 304 "not modified"
- 305 "use proxy"
- 307 "temporary redirect"
- 308 "permanent redirect"
- 400 "bad request"
- 401 "unauthorized"
- 402 "payment required"
- 403 "forbidden"
- 404 "not found"
- 405 "method not allowed"
- 406 "not acceptable"
- 407 "proxy authentication required"
- 408 "request timeout"
- 409 "conflict"
- 410 "gone"
- 411 "length required"
- 412 "precondition failed"
- 413 "payload too large"
- 414 "uri too long"
- 415 "unsupported media type"
- 416 "range not satisfiable"
- 417 "expectation failed"
- 418 "I'm a teapot"
- 422 "unprocessable entity"
- 423 "locked"
- 424 "failed dependency"
- 426 "upgrade required"
- 428 "precondition required"
- 429 "too many requests"
- 431 "request header fields too large"
- 500 "internal server error"
- 501 "not implemented"
- 502 "bad gateway"
- 503 "service unavailable"
- 504 "gateway timeout"
- 505 "http version not supported"
- 506 "variant also negotiates"
- 507 "insufficient storage"
- 508 "loop detected"
- 510 "not extended"
- 511 "network authentication required"



## message

响应状态码附属的消息，默认为状态码对应的信息



## length

获取响应对象的`Content-Length`

不存在该字段则返回`undefined`



## type

获取`Content-Type`，不含`charset`等参数（其实是获取 *mime-type*，[源码](https://github.com/koajs/koa/blob/eda27608f7d39ede86d7b402aae64b1867ce31c6/lib/request.js#L639)）



## body

响应体，其内容只能是以下数据类型：

+ string
+ Buffer
+ Stream piped
+ Object || Array json-stringified
+ null || undefined

```js
// 关于 stream 的示例
const PassThrough = require('stream').PassThrough

app.use(async ctx => {
  ctx.body = someHTTPStream.on('error', err => ctx.onerror(err)).pipe(PassThrough())
})
```



## get( )

`response.get(field)`可以获取响应头指定字段的值，`field`不区分大小写



## has( )

`response.has(field)`可以判断响应头是否包含对应的字段，`field`不区分大小写



## set( )

`response.set(field, value)`可以在响应头中设置字段键值对

```js
ctx.response.set('Content-Type', 'application/json')
ctx.response.set('X-Custom-Header', ['value1', 'value2']) // 设置多值字段
```



`response.set(fields)`可以通过一个`fields`对象`{}`设置一系列字段

```js
ctx.set({
    'Etag': '1234',
    'Last-Modified': date
})
```



## append( )

`response.append(field, value)`可以向指定字段新增值

如果字段不存在则新建字段

如果字段已存在则将值追加到现有值后，多个值以`,`分隔（如果字段支持多值）



## remove( )

`response.remove(field)`可以移除字段



## is( )

`response.is(types...)`和`request.is()`几乎一致

检查响应类型是否为支持的类型之一



## redirect( )

`response.redirect(url[, alt])`可进行重定向操作

默认状态码为 302，然后通过`Location`响应头指示新的 url

如果需要手动修改状态码，需要在`redirect()`前进行操作

+ 301 永久重定向（Moved Permanently）
+ 302 临时重定向（Found，默认）
+ 303 重定向后应该使用 `GET` 方法（See Other）
+ 307 临时重定向，保持原始请求方法（Temporary Redirect）

`alt`参数通常用于提供不同类型的重定向状态码

当`url`为`'back'`时，则浏览器会返回上一个页面，这是通过检查请求头的`Referer`字段实现的

如果不存在`Referer`，则会将请求重定向到`alt`参数提供的备用 url，如果`alt`没有提供则重定向到根目录`/`



## attachment( )

`response.attachment([filename], [options])`设置响应头，使浏览器将响应内容作为**下载文件**处理，而不是在浏览器中显示，用于实现文件下载功能

`filename`作为文件的默认下载名称，不提供时浏览器使用响应的 url 作为文件名

`options`：

+ `type`：指定下载文件的 mime-type，未指定则 Koa 自动推断类型
+ `filename`：同第一个参数
+ `disposition`：指定下载的方式，通常为`attachment`（强制下载），如果为`inline`则会让浏览器尽量尝试在浏览器中打开文件而非下载

```js
app.use(async (ctx) => {
    const filePath = path.join(__dirname, 'example.txt')
    const fileStream = fs.createReadStream(filePath)

    ctx.response.attachment('download.txt', { disposition: 'attachment' }) // 下载名称
    ctx.body = fileStream // 响应文件内容
})
```





## headerSent

检查响应头是否已经被发送，可以避免响应头在发送后再进行修改



## lastModified

以`Date`对象的形式获取`Last-Modified`字段



## etag

实体标签，可以标识资源版本

```js
ctx.response.etag = crypto.createHash('md5').update(ctx.body).digest('hex')
```



## vary( )

`response.vary(field)`可以修改响应头`Vary`字段

`Vary` 头部是 HTTP 缓存控制机制的一部分，指示哪些请求头字段（例如 `Accept-Encoding`, `User-Agent`, `Accept-Language` 等）会影响响应的内容。当服务器返回的内容会根据某些请求头字段的不同值有所不同时，`Vary` 头部告知缓存服务器或浏览器如何缓存和复用响应内容

如果客户端的请求头或缓存机制中有与 `Vary` 头部一致的字段，那么缓存服务器将根据这个字段来决定是否使用缓存中的数据，或者是否重新发起请求

```js
app.use(async (ctx) => {
    ctx.response.vary('Accept-Encoding') // 指示缓存根据 Accept-Encoding 进行缓存
    ctx.body = 'Hello, Koa!'
})
```





## flushHeaders( )

`response.flushHeaders()`用于立即将响应头发送给客户端，无需等待响应体发送

用于希望尽早处理响应头的场合

实现类似流式传输或者与大文件传输相关的场景非常有用



# Package

## 路由

[GitHub 仓库](https://github.com/koajs/router)

[API.md](https://github.com/koajs/router/blob/master/API.md)

### 引入

```shell
npm i @koa/router
```

```js
const Router = require('@koa/router')
const router = new Router()
// ...
app
    .use(router.routes())
 // 为没有匹配的请求返回 405 Not Allowed
// 或是 404 Not Found
    .use(router.allowedMethods())
```



## 静态资源

[GitHub 仓库](https://github.com/koajs/static)

### 引入

```shell
npm i koa-static
```

```js
const KoaStatic = require('koa-static')

app.use(KoaStatic(path.join(__dirname, 'public')), opts)
```



## 虚拟路径

[GitHub 仓库](https://github.com/koajs/mount)

### 引入

```shell
npm i koa-mount
```

```js
const KoaMount = require('koa-mount')

app.use(KoaMount('/prefix', middleware))
```



## 合并中间件

[GitHub 仓库](https://github.com/koajs/compose)

### 引入

```shell
npm i koa-compose
```

```js
const compose = require('koa-compose')

// 将多个中间件函数一并导入
app.use(compose([middleware_1, middleware_2, ...]))
```



## 请求体解析

### koa-bodyparser

#### 引入

```shell
npm install koa-bodyparser
```

```js
const Koa = require('koa')
const bodyParser = require('koa-bodyparser')
const app = new Koa()

app.use(bodyParser())

app.use(async (ctx) => {
    if (ctx.method === 'POST') {
        ctx.body = {
            message: 'Received POST data',
            data: ctx.request.body, // 获取解析后的请求体
        }
    } else {
        ctx.body = 'Send a POST request'
    }
})
```



### koa-body

#### 引入

```shell
npm install koa-body
```

```js
const Koa = require('koa')
const koaBody = require('koa-body')
const app = new Koa()

app.use(koaBody({
    multipart: true, // 支持文件上传
    formidable: {
        uploadDir: './uploads', // 设置文件上传目录
        keepExtensions: true,  // 保留文件扩展名
    },
}))

app.use(async (ctx) => {
    if (ctx.method === 'POST') {
        ctx.body = {
            message: 'Received POST data',
            data: ctx.request.body, // 请求体内容
            files: ctx.request.files, // 上传的文件（如果有）
        }
    } else {
        ctx.body = 'Send a POST request'
    }
})
```

