# 官网

[点击进入中文版官网](https://www.axios-http.cn/)



# 下载

`npm`

```shell
npm install axios
```

`cdn`

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```



# 基本请求

## Get

```js
// url  必须提供
// conf 可选
axios.get('{{ url }}', {
    params: {
        id: '001'
    }
}).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
}).finally(() => {
    // ...
})
```



## Post

```js
axios.post('{{ url }}', {
    params: {
        id: '001'
    }
}).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
}).finally(() => {
    // ...
})
```

将 From 转为 Json 进行请求：

```js
axios.post('{{ url }}', document.querySelector('#my-form'), {
    headers: {
        'Content-Type': 'application/json'
    }
})
```

`multipart/form-data`

```js
axios.post('{{ url }}', {
    id: '001',
    file: document.querySelector('#fileInput').files
}, {
    headers: {
        'Content-Type': 'multipart/form-data'
    }
})
```

`application/x-www-form-urlencoded`

```js
axios.post('{{ url }}', {
    id: '001',
    arr: [1, 4, 5, 2, 3]
}, {
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded'
    }
})
```



# API

`axios(config)`

`axios(url, [, config])`

```js
axios({
    method: 'get',
    url: '{{ url }}'
    data: {
        id: '001'
    }
})
```

请求方式别名：

`axios.request(config)`

`axios.get(url[, config])`

`axios.delete(url[, config])`

`axios.head(url[, config])`

`axios.options(url[, config])`

`axios.post(url[, data[, config]])`

`axios.put(url[, data[, config]])`

`axios.patch(url[, data[, config]])`

`axios.postForm(url[, data[, config]])`

`axios.putForm(url[, data[, config]])`

`axios.patchForm(url[, data[, config]])`

`postForm`，`putForm`，`patchForm`方法是对应的 HTTP 方法的别称，区别在于`content-type`默认为`multipart/form-data`

# Axios 实例

可以使用自定义配置自行创建一个`axios`实例

`axios.create([config])`

```js
const instance = axios.create({})
```

实例将包含默认配置，并结合到`axios`实例方法中：

`instance.request(config)`

`instance.get(url[, config])`

`instance.delete(url[, config])`

`instance.head(url[, config])`

`instance.options(url[, config])`

`instance.post(url[, data[, config]])`

`instance.put(url[, data[, config]])`

`instance.patch(url[, data[, config]])`

`instance.getUri([config])`



# Config

[请求配置项](https://www.axios-http.cn/docs/req_config)

```js
{
    url: '/user', // `url` 是用于请求的服务器 URL

    method: 'get', // `method` 是创建请求时使用的方法 默认值为'get'

    // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
    // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
    baseURL: 'https://some-domain.com/api/',

    // `transformRequest` 允许在向服务器发送前，修改请求数据
    // 它只能用于 'PUT', 'POST' 和 'PATCH' 这几个请求方法
    // 数组中最后一个函数必须返回一个字符串， 一个Buffer实例，ArrayBuffer，FormData，或 Stream
    // 你可以修改请求头
    transformRequest: [function (data, headers) {
        // 对发送的 data 进行任意转换处理

        return data
    }],

    // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
    transformResponse: [function (data) {
        // 对接收的 data 进行任意转换处理

        return data
    }],

    // 自定义请求头
    headers: { 'X-Requested-With': 'XMLHttpRequest' },

    // `params` 是与请求一起发送的 URL 参数
    // 必须是一个简单对象或 URLSearchParams 对象
    params: {
        ID: 12345
    },

    // `paramsSerializer`是可选方法，主要用于序列化`params`
    // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
    paramsSerializer: function (params) {
        return Qs.stringify(params, { arrayFormat: 'brackets' })
    },

    // `data` 是作为请求体被发送的数据
    // 仅适用 'PUT', 'POST', 'DELETE 和 'PATCH' 请求方法
    // 在没有设置 `transformRequest` 时，则必须是以下类型之一:
    // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
    // - 浏览器专属: FormData, File, Blob
    // - Node 专属: Stream, Buffer
    data: {
        firstName: 'Fred'
    },

    // 发送请求体数据的可选语法
    // 请求方式 post
    // 只有 value 会被发送，key 则不会
    data: 'Country=Brasil&City=Belo Horizonte',

    // `timeout` 指定请求超时的毫秒数。
    // 如果请求时间超过 `timeout` 的值，则请求会被中断
    timeout: 1000, // 默认值是 `0` (永不超时)

    // `withCredentials` 表示跨域请求时是否需要使用凭证
    withCredentials: false, // default

    // `adapter` 允许自定义处理请求，这使测试更加容易。
    // 返回一个 promise 并提供一个有效的响应 （参见 lib/adapters/README.md）。
    adapter: function (config) {
        /* ... */
    },

    // `auth` HTTP Basic Auth
    auth: {
        username: 'janedoe',
        password: 's00pers3cret'
    },

    // `responseType` 表示浏览器将要响应的数据类型
    // 选项包括: 'arraybuffer', 'document', 'json', 'text', 'stream'
    // 浏览器专属：'blob'
    responseType: 'json', // 默认值

    // `responseEncoding` 表示用于解码响应的编码 (Node.js 专属)
    // 注意：忽略 `responseType` 的值为 'stream'，或者是客户端请求
    // Note: Ignored for `responseType` of 'stream' or client-side requests
    responseEncoding: 'utf8', // 默认值

    // `xsrfCookieName` 是 xsrf token 的值，被用作 cookie 的名称
    xsrfCookieName: 'XSRF-TOKEN', // 默认值

    // `xsrfHeaderName` 是带有 xsrf token 值的http 请求头名称
    xsrfHeaderName: 'X-XSRF-TOKEN', // 默认值

    // `onUploadProgress` 允许为上传处理进度事件
    // 浏览器专属
    onUploadProgress: function (progressEvent) {
        // 处理原生进度事件
    },

    // `onDownloadProgress` 允许为下载处理进度事件
    // 浏览器专属
    onDownloadProgress: function (progressEvent) {
        // 处理原生进度事件
    },

    // `maxContentLength` 定义了node.js中允许的HTTP响应内容的最大字节数
    maxContentLength: 2000,

    // `maxBodyLength`（仅Node）定义允许的http请求内容的最大字节数
    maxBodyLength: 2000,

    // `validateStatus` 定义了对于给定的 HTTP状态码是 resolve 还是 reject promise。
    // 如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，
    // 则promise 将会 resolved，否则是 rejected。
    validateStatus: function (status) {
        return status >= 200 && status < 300 // 默认值
    },

    // `maxRedirects` 定义了在node.js中要遵循的最大重定向数。
    // 如果设置为0，则不会进行重定向
    maxRedirects: 5, // 默认值

    // `socketPath` 定义了在node.js中使用的UNIX套接字。
    // e.g. '/var/run/docker.sock' 发送请求到 docker 守护进程。
    // 只能指定 `socketPath` 或 `proxy` 。
    // 若都指定，这使用 `socketPath` 。
    socketPath: null, // default

    // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
    // and https requests, respectively, in node.js. This allows options to be added like
    // `keepAlive` that are not enabled by default.
    httpAgent: new http.Agent({ keepAlive: true }),
    httpsAgent: new https.Agent({ keepAlive: true }),

    // `proxy` 定义了代理服务器的主机名，端口和协议。
    // 您可以使用常规的`http_proxy` 和 `https_proxy` 环境变量。
    // 使用 `false` 可以禁用代理功能，同时环境变量也会被忽略。
    // `auth`表示应使用HTTP Basic auth连接到代理，并且提供凭据。
    // 这将设置一个 `Proxy-Authorization` 请求头，它会覆盖 `headers` 中已存在的自定义 `Proxy-Authorization` 请求头。
    // 如果代理服务器使用 HTTPS，则必须设置 protocol 为`https`
    proxy: {
        protocol: 'https',
        host: '127.0.0.1',
        port: 9000,
        auth: {
            username: 'mikeymike',
            password: 'rapunz3l'
        }
    },

    // see https://axios-http.com/zh/docs/cancellation
    cancelToken: new CancelToken(function (cancel) {
    }),

    // `decompress` indicates whether or not the response body should be decompressed 
    // automatically. If set to `true` will also remove the 'content-encoding' header 
    // from the responses objects of all decompressed responses
    // - Node only (XHR cannot turn off decompression)
    decompress: true // 默认值
}
```



# Response

[响应结构](https://www.axios-http.cn/docs/res_schema)

```js
{
    // `data` 由服务器提供的响应
    data: {},

    // `status` 来自服务器响应的 HTTP 状态码
    status: 200,

    // `statusText` 来自服务器响应的 HTTP 状态信息
    statusText: 'OK',

    // `headers` 是服务器响应头
    // 所有的 header 名称都是小写，而且可以使用方括号语法访问
    // 例如: `response.headers['content-type']`
    headers: {},

    // `config` 是 `axios` 请求的配置信息
    config: {},

    // `request` 是生成此响应的请求
    // 在node.js中它是最后一个ClientRequest实例 (in redirects)，
    // 在浏览器中则是 XMLHttpRequest 实例
    request: {}
}
```



# 默认配置

## 全局 axios

```js
axios.defaults.baseURL = 'https://api.example.com'
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'
```



## 自定义实例

```js
// 创建实例时配置默认值
const instance = axios.create({
  baseURL: 'https://api.example.com'
})

// 创建实例后修改默认值
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN
```



## 优先级

配置将会按优先级进行合并。它的顺序是：在[lib/defaults.js](https://github.com/axios/axios/blob/master/lib/defaults.js#L28)中找到的库默认值，然后是实例的`defaults`属性，最后是请求的`config`参数

```js
// 使用库提供的默认配置创建实例
// 此时超时配置的默认值是 `0`
const instance = axios.create()

// 重写库的超时默认值
// 现在，所有使用此实例的请求都将等待2.5秒，然后才会超时
instance.defaults.timeout = 2500

// 重写此请求的超时时间，因为该请求需要很长时间
instance.get('/longRequest', {
  timeout: 5000
})
```



# 拦截器

拦截器会在请求或响应处理前，作为中间件先行处理数据

```js
const requestInterceptor = axios.interceptors.request.use(config => {
    // ...
    return config
}, err => {
    return Promise.reject(error)
})

const responseInterceptor = axios.interceptors.response.use(res => {
    // 2xx 范围内的状态码都会触发该函数
    // ...
    return res
}, err => {
    // 超出 2xx 范围的状态码都会触发该函数
    return Promise.reject(error)
})

// 如果需要移除拦截器
axios.interceptors.request.eject(requestInterceptor)
axios.interceptors.request.eject(responseInterceptor)
```



# 错误处理

```js
axios.get('{{ url }}').catch(function (error) {
    if (error.response) {
        // 请求成功发出且服务器也响应了状态码，但状态代码超出了 2xx 的范围
        console.log(error.response.data)
        console.log(error.response.status)
        console.log(error.response.headers)
    } else if (error.request) {
        // 请求已经成功发起，但没有收到响应
        // error.request 在浏览器中是 XMLHttpRequest 的实例，
        // 而在 node.js 中是 http.ClientRequest 的实例
        console.log(error.request)
    } else {
        // 发送请求时出了点问题
        console.log('Error', error.message)
    }
    console.log(error.config)
})
```

使用 `validateStatus` 配置选项，可以自定义抛出错误的 HTTP code

```js
axios.get('{{ url }}', {
    validateStatus: function (status) {
        return status < 500 // 处理状态码小于500的情况
    }
})
```

使用 `toJSON` 可以获取更多关于HTTP错误的信息

```js
axios.get('{{ url }}').catch(function (error) {
    console.log(error.toJSON())
})
```



# 取消请求

## `AbortController`

从 `v0.22.0` 开始，Axios 支持以 fetch API 方式—— [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 取消请求

```js
const controller = new AbortController()

axios.get('{{ url }}', {
   signal: controller.signal
}).then(function(response) {
   //...
})
// 取消请求
controller.abort()
```



## CancelToken`deprecated`

可以使用 *cancel token* 取消一个请求

> Axios 的 cancel token API 是基于被撤销 [cancelable promises proposal](https://github.com/tc39/proposal-cancelable-promises)

> 此 API 从 `v0.22.0` 开始已被弃用，不应在新项目中使用

可以使用 `CancelToken.source` 工厂方法创建一个 cancel token

```js
const CancelToken = axios.CancelToken
const source = CancelToken.source()

axios.get('{{ url }}', {
    cancelToken: source.token
}).catch(function (thrown) {
    if (axios.isCancel(thrown)) {
        console.log('Request canceled', thrown.message)
    } else {
        // 处理错误
    }
})

axios.post('{{ url }}', {
    name: 'new name'
}, {
    cancelToken: source.token
})

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.')
```

也可以通过传递一个 executor 函数到 `CancelToken` 的构造函数来创建一个 cancel token

```js
const CancelToken = axios.CancelToken
let cancel

axios.get('/user/12345', {
    cancelToken: new CancelToken(function executor(c) {
        // executor 函数接收一个 cancel 函数作为参数
        cancel = c
    })
})

// 取消请求
cancel()
```

> 注意: 可以使用同一个 cancel token 或 signal 取消多个请求

在过渡期间，您可以使用这两种取消 API，即使是针对同一个请求

```js
const controller = new AbortController()

const CancelToken = axios.CancelToken
const source = CancelToken.source()

axios.get('/user/12345', {
    cancelToken: source.token,
    signal: controller.signal
}).catch(function (thrown) {
    if (axios.isCancel(thrown)) {
        console.log('Request canceled', thrown.message)
    } else {
        // 处理错误
    }
})

axios.post('/user/12345', {
    name: 'new name'
}, {
    cancelToken: source.token
})

// 取消请求 (message 参数是可选的)
source.cancel('Operation canceled by the user.')
// 或
controller.abort() // 不支持 message 参数
```



# application/x-www-form-urlencoded

默认情况下，axios将 JavaScript 对象序列化为`JSON`

以`application/x-www-form-urlencoded`格式发送数据需要使用以下选项之一



## 自动序列化

请求头中`content-type: application/x-www-form-urlencoded`时，Axios 将自动将普通对象序列化成 urlencoded 的格式

如果服务器框架的请求体解析器（例如`express.js`的`body-parser`）支持嵌套对象解码，则其接收到的数据将与您提交的数据一样

`request`

```js
const data = {
    x: 1,
    arr: [1, 2, 3],
    arr2: [1, [2], 3],
    users: [
        {name: 'Peter', surname: 'Griffin'},
        {name: 'Thomas', surname: 'Anderson'}
    ]
}

await axios.post(
    '{{ url }}',
    data,
    { headers: {'content-type': 'application/x-www-form-urlencoded'} }
)
```

`server`

```js
{
    x: '1',
    'arr[]': [ '1', '2', '3' ],
    'arr2[0]': '1',
    'arr2[1][0]': '2',
    'arr2[2]': '3',
    'arr3[]': [ '1', '2', '3' ],
    'users[0][name]': 'Peter',
    'users[0][surname]': 'griffin',
    'users[1][name]': 'Thomas',
    'users[1][surname]': 'Anderson'
}
```



## 浏览器

### [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)

```js
const params = new URLSearchParams()
params.append('param1', 'value1')
params.append('param2', 'value2')
axios.post('/foo', params)
```



### qs

借助[qs](https://github.com/ljharb/qs?tab=readme-ov-file)库编码数据

```js
const qs = require('qs')
// import qs from 'qs'

axios.post('/foo', qs.stringify({ 'bar': 123 }))
```



## NodeJS

### Query string

```js
const querystring = require('querystring')
axios.post('http://something.com/', querystring.stringify({ foo: 'bar' }))
```



### URLSearchParams

```js
const url = require('url')
// 从 url 模块获取 URLSearchParams
const params = new url.URLSearchParams({ foo: 'bar' })
axios.post('http://something.com/', params.toString())
```



### qs

同上



### from-data

借助[form-data](https://github.com/form-data/form-data)库

```shell
npm i form-data
```

```js
const FormData = require('form-data')
 
const form = new FormData()
form.append('my_field', 'my value')
form.append('my_buffer', new Buffer(10))
form.append('my_file', fs.createReadStream('/foo/bar.jpg'))

axios.post('https://example.com', form, { headers: form.getHeaders() })
```



# Multipart 实体请求

使用 `multipart/form-data` 类型发起 `POST` 请求



## 自动序列化

从`v0.27.0`版本开始，当请求头中的 Content-Type 是`multipart/form-data`时，Axios 支持自动地将普通对象序列化成一个 FormData 对象

Axios FormData 序列化器支持一些特殊的结尾：

- `{}`：通过 `JSON.stringify` 序列化数据
- `[]`：将 array-like 的对象使用相同的键值来展开为单独的字段

序列化器支持通过`config.formSerializer: object`参数配置以支持特殊情况：

- `visitor: Function`：程序将递归调用，按自定义规则序列化数据

- `dots: boolean = false`：使用`.`而不是括号来序列化数组和对象

- `metaTokens: boolean = true`：在 FormData 键值中添加特殊结尾（例如`user{}: '{"name": "John"}'`），后端的 body-parser 可能会使用此元信息将值自动解析为 JSON

- `indexes: null|false|true = false`：array-like 对象的展开方式
  - `null`：不添加中括号（`arr: 1`，`arr: 2`，`arr: 3`）
  - `false`：添加空中括号（`arr[]: 1`，`arr[]: 2`，`arr[]: 3`）
  - `true`：添加带有索引的中括号（`arr[0]: 1`，`arr[1]: 2`，`arr[2]: 3`）

例如发送方：

```js
const obj = {
    x: 1,
    arr: [1, 2, 3],
    arr2: [1, [2], 3],
    users: [
        {name: 'Peter', surname: 'Griffin'},
        {name: 'Thomas', surname: 'Anderson'}
    ],
    'obj2{}': [ { x:1 } ]
}
```

接下来将执行序列化：

```js
const formData = new FormData()
formData.append('x', '1')
formData.append('arr[]', '1')
formData.append('arr[]', '2')
formData.append('arr[]', '3')
formData.append('arr2[0]', '1')
formData.append('arr2[1][0]', '2')
formData.append('arr2[2]', '3')
formData.append('users[0][name]', 'Peter')
formData.append('users[0][surname]', 'Griffin')
formData.append('users[1][name]', 'Thomas')
formData.append('users[1][surname]', 'Anderson')
formData.append('obj2{}', '[{"x":1}]')
```

此外，`FileList`对象可以直接传输，所有文件将以相同字段`files[]`发送

```js
await axios.postForm('{{ url }}', document.querySelector('#fileInput').files)
```



## FormData

Axios 版本小于`v1.3.0`时必须手动引入`form-data`

```js
const form = new FormData()
form.append('my_field', 'my value')
form.append('my_buffer', new Blob([1,2,3]))
form.append('my_file', fileInput.files[0])

axios.post('https://example.com', form)

// 等同于
// 自动序列化
axios.postForm('https://httpbin.org/post', {
    my_field: 'my value',
    my_buffer: new Blob([1,2,3]),
    my_file:  fileInput.files // FileList will be unwrapped as sepate fields
})
```

由于 node.js 不支持从文件创建`Blob`，因此可以借助第三方软件包

```js
import { fileFromPath } from 'formdata-node/file-from-path'

form.append('my_field', 'my value')
form.append('my_file', await fileFromPath('/foo/bar.jpg')) // 传输

axios.post('https://example.com', form)
```

